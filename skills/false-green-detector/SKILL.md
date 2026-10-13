---
name: false-green-detector
description: Review existing tests against specifications, checking how expected results are derived and whether assertions detect incorrect behavior. Use for specification-based test reviews or requests to find tests that pass despite bugs or merely mirror the implementation.
---

# False Green Detector

Review whether expected results come from the specification and whether tests catch behavior that violates it. A test and its implementation can agree while both contradict the specification.

## 1. Derive expected behavior from the specification

Read the applicable requirements and API or feature specifications. Describe each behavior as **preconditions → input or action → expected outcome**. Depending on the feature, outcomes include return values, persisted data, state changes, and requests sent to external systems.

For example, suppose the specification says: “Accept an order and deduct its quantity when stock is sufficient; otherwise reject it and leave stock unchanged.” With 3 items in stock, ordering 2 should succeed and leave 1. Ordering 4 should fail and leave 3. Establish these success and failure expectations before comparing them with tests. The examples below illustrate review techniques; derive actual expectations from the specification under review.

## 2. Compare expectations with what tests actually verify

For each expected outcome, find the assertion that checks it and read the test setup and execution path. A test that runs “change address → create order → pay” but only checks payment success still needs an assertion that the order uses the new address. In tests with several actions, identify where each outcome is checked.

- **Source of expected values:** Is the expected value calculated or chosen from the specification? Suppose order calculation uses a discount function, and the test calls that same function to calculate its expected total. If the function applies 20% instead of 10%, both values become 8,000 and the test passes. If the specification says “10% off 10,000,” compare the actual total with the independently derived value of 9,000 to catch this error.

- **Preconditions:** Does the setup create the intended situation? A test for rejecting expired coupons might use a coupon code that does not exist. It would then reject the coupon even if expiration checking were removed. Use an existing coupon that meets all other conditions, make only its validity period expire, and check the specified expiration outcome.

- **Observable results:** Did the required operation actually happen? An address-change test that only checks a success response can pass even if persistence is skipped. Change address A to B, read the stored address again, and check that it is B. Starting with B already stored makes it difficult to distinguish a successful update from doing nothing.

- **Prior state and sequences of actions:** Does the next action correctly update state left by an earlier one? Suppose adding a new shipping address must clear the previous default address flag. Testing only customers with no addresses would miss code that fails to clear that flag. Start with default address A, add B, and check that B becomes the default and A loses that status. Alongside tests starting from an empty state, examine the specified flows for changes, retries, and actions after expiration.

- **Distinguishing inputs:** Do tests distinguish outcomes that should depend on the input? If customers A and B have the same shipping address in the fixtures, using A's address for B's order may still pass. Give them different addresses and check that the ordering customer's address is used. Check boundaries too: if the specification allows at most 5 items, verify that 4 and 5 are accepted and 6 is rejected.

- **Scope of test doubles:** Does a mock or stub replace the very behavior being tested? A test for rejecting orders with insufficient stock does not exercise stock comparison if it replaces the order decision function with a stub that always returns “insufficient stock.” Instead, have the stock-reading dependency return 1 and ask the real order logic to order 2, then check rejection. If the test only covers how a caller propagates an error, check whether another test covers the stock decision itself.

Check cleanup required by the specification on failure paths. If canceling a download must stop an active transfer, checking that the caller receives “canceled” can miss a transfer that keeps running. Also check that the test double at the transport boundary received a cancellation request, or that the actual transfer stopped. Make this observation before test teardown closes the connection on the implementation's behalf.

Match assertions to the level of detail required by the specification. If only the sorted result is specified, requiring exactly three calls to an internal comparator can break a valid change of sorting algorithm. Check the resulting order instead. If the specification explicitly requires a payment request to be sent exactly once, the number of sends is an appropriate assertion.

## 3. Check whether incorrect behavior would be caught

Read the tests, implementation, and callers together and ask: **“Could an implementation that violates this specification still pass these tests?”** For example, tests covering only insufficient stock would also pass an implementation that rejects every order. Look for a test showing that sufficient stock allows the order and reduces inventory. Account for coverage provided by other tests.

When the answer is unclear, run the relevant tests to establish a baseline, then deliberately break the behavior in a temporary copy. If tests still pass after stock deduction is removed, they miss that effect. A failure saying “expected 1 remaining item, but found 3” demonstrates detection. A compilation error or database connection failure does not establish whether stock deduction was checked.

## 4. Distinguish test gaps from implementation defects

- **Test gap:** The specification requires stock deduction after an order, but the test only checks a success response. Even if the implementation is currently correct, the test can miss a regression that removes the deduction.

- **Implementation defect:** The specification requires a 10% discount, but the code applies 20%. If the test also expects the 20% result, report both the calculation defect and the incorrect test expectation.

- **Specification needs clarification:** The specification does not say whether a coupon and a membership discount can be combined, so the expected total cannot be determined. Explain which policy must be resolved to establish an expectation.

For each finding, provide the specification evidence, code location, input and outcome that expose the problem, and a suggested correction. Order findings by impact and state which checks were actually run and what remains unverified.

If fixes are also requested, correct expected values and fixtures using the specification. For the discount example, first change the expectation to 9,000 and confirm that the implementation's 8,000 result fails. Then fix the implementation to apply 10% and rerun the affected and related tests.
