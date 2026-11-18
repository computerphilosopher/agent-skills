# 지급 내역 기술 명세 발췌

## 지급 필드

| 필드 | 형식 또는 의미 |
| --- | --- |
| `amount` | 원 단위 `bigint` |
| `settlementId` | PK, FK |
| `receiptNo` | `text`, null 허용. 승인 응답에서 얻은 접수번호 |

## 조회 기록 필드

| 필드 | 저장 정보 |
| --- | --- |
| `id` | `uuid` |
| `checkedAt` | 결과를 마지막으로 조회한 시각 |
| `resultCode` | 공급사가 반환한 결과 코드 |

## 조회 메서드

| 메서드 | 입력 | 반환 | 역할 |
| --- | --- | --- | --- |
| `findPayout` | `payoutId: UUID` | `Payout` | 지급 내역을 조회한다. |
