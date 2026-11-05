---
name: munjang-ganghwa
description: Use when a user asks for a comprehensive document review or revision covering unnecessary content, document structure, and sentence expression, or invokes 문장강화.
---

# 문장강화

문서 정리·구성·표현 검토를 순서대로 연결하고 결과를 통합한다. 각 단계의 판단 기준은 해당 스킬에서 읽는다.

## 호출

| 호출 | 모드 |
| --- | --- |
| `$munjang-ganghwa 문서경로` | 리뷰: 원문을 유지하고 발견 사항을 보고한다. |
| `$munjang-ganghwa fix 문서경로` | 수정: 각 단계의 수정 사항을 문서에 반영한다. |

기본은 리뷰다. “수정해”, “정리해”, “반영해”처럼 실제 편집을 요청해도 수정 모드를 선택한다. 사용자가 지정한 대상·범위·모드를 각 단계에 동일하게 전달한다.

## 실행

범위 지정이 없으면 세 단계를 모두 수행한다. 특정 관점만 요청받으면 해당 단계를 선택하고, 선택한 단계는 아래 순서로 진행한다. 각 단계 시작 시 연결된 `SKILL.md`를 읽고 적용한다.

| 순서 | 스킬 | 담당 |
| --- | --- | --- |
| 1 | [document-thanos](../document-thanos/SKILL.md) | 불필요한 내용 삭제와 중복 통합 |
| 2 | [document-structure-review](../document-structure-review/SKILL.md) | 설명의 충실도, 장절 위계, 정보 배치, 표와 절차 연결 |
| 3 | [sentence-expression-review](../sentence-expression-review/SKILL.md) | 문장의 의미 전달과 표현 |

리뷰 모드에서는 각 단계에 검토만 요청하고 파일을 유지한다. 발견 사항과 제안을 모아 마지막에 한 번 보고한다.

수정 모드에서는 각 단계가 직전 단계의 수정본을 다시 읽고 편집을 이어간다. 근거가 충분한 부분은 수정하고, 새 정책이나 설계 결정이 필요한 부분은 질문으로 남긴다. 마지막 수정본의 의미·조건·참조를 확인하고 대상 저장소가 요구하는 관련 검사를 실행한다.

## 결과 보고

리뷰에서는 영향이 큰 발견 사항부터 위치·영향·수정 방향을 제시한다. 같은 원인의 지적은 묶고, 추가 결정이 필요한 항목과 검토 범위를 밝힌다.

수정에서는 실제 변경과 의미 보존 확인 결과, 남은 질문을 짧게 보고한다. 필요한 스킬이나 자료를 읽지 못한 단계는 미검토로 표시한다.
