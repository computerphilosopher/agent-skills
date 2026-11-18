# 점주 지급 안내

[자동 지급의 목적](payout-references.md#지급-목적) · [지급 상태의 의미](payout-references.md#지급-상태)

이 안내는 검토용이며 아직 확정되지 않았습니다. 단정할 수 없어 확인이 필요하고 미결로 남깁니다.

## 자동 지급

최초 자동 지급 기준일 당일에 서버가 지급을 요청합니다. 판매 대금을 점주 계좌로 보냅니다. 송금 요청을 접수하면 처리 중으로 표시합니다.

[잔액의 차감·해제 조건과 지급액 외 추가 비용](payout-references.md#공급사-질문)은 공급사에서 확인한 뒤 운영 자동 송금을 시작합니다. 서버는 [수취 계좌를 검증한 뒤](payout-references.md#수취-계좌-검증) 지급을 요청합니다.

### 한 MID의 요청 제어

서버는 같은 `MID`에 진행 중인 요청이 있으면 `SKIPPED`를 반환합니다. 진행 중인 요청이 없으면 잔액을 확인하고, 잔액이 `0`이면 `SKIPPED`를 반환합니다.

| 구성요소 | 메서드 | 입력 | 반환 |
| --- | --- | --- | --- |
| `Scheduler` | `run` | `request: Request` | `Result` |
| `Gateway` | `send` | `mid: String` | `void` |

```text
Scheduler.run(request):
    if hasPendingRequest(request.mid):
        return SKIPPED
    gateway.send(request.mid)
    return PROCESSING
```

| 자료형 | 필드 또는 값 |
| --- | --- |
| `Request` | `mid: String`은 상점 식별자, `balance: Long`은 원 단위 잔액 |
| `Result` | `SKIPPED`는 요청 생략, `PROCESSING`은 접수 완료 |

## 테스트 데이터 정리

정리 명령은 개발용이고 보류로 남깁니다. 운영 환경에서 실행하면 `403`이 반환됩니다. 개발 환경에서는 이 명령으로 테스트 데이터를 삭제합니다.

[운영 명령 응답](payout-references.md#운영-응답)
[개발 환경 정리](payout-references.md#개발-환경)

## 한 관리자의 내역 조회

관리자는 하나의 정산 화면에서 내역을 확인할 수 있습니다. 관리자 계정마다 하루 한 번 정산 내역을 내려받을 수 있습니다. 같은 날 두 번째 다운로드를 요청하면 서버가 `429`를 반환합니다.

## 빠른 이동

- [지급 요청](#한-mid의-요청-제어)
- [테스트 데이터 정리](#테스트-데이터-정리)
