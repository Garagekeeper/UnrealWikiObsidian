Assert 매크로, [[check(expreesion)]]계열과 동일하게 동작하지만, shipping에서도 포함된다는 차이가 있음. Slow가 붙은 Verify는 예외적으로 디버그 빌드에서만 작동

| 매크로                        | 파라미터                                   | 동작                                                             |
| -------------------------- | -------------------------------------- | -------------------------------------------------------------- |
| `verify` 또는 `verifySlow`   | `Expression`                           | `Expression` 이 false인 경우 실행을 정지합니다.                            |
| `verifyf` or `verifyfSlow` | `Expression` , `FormattedText` , `...` | `Expression` 이 false인 경우 실행을 정지하고 로그에 `FormattedText` 를 출력합니다. |

[UE_Docs](https://dev.epicgames.com/documentation/unreal-engine/asserts-in-unreal-engine)