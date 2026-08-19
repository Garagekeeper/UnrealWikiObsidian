Assert 매크로, expreesion 결과가 false이면 실행 중지 (프로그램 종료), 기본적으로 출시 빌드에서 실행되지 않는다. Slow가 붙은 check 예외적으로 디버그 빌드에서만 작동

| 매크로                      | 파라미터                                   | 동작                                                                            |
| ------------------------ | -------------------------------------- | ----------------------------------------------------------------------------- |
| `check` 또는 `checkSlow`   | `Expression`                           | `Expression` 이 false인 경우 실행 정지됩니다.                                            |
| `checkf` 또는 `checkfSlow` | `Expression` , `FormattedText` , `...` | `Expression` 이 false인 경우 실행 정지되고 로그에 `FormattedText` 를 출력합니다.                 |
| `checkCode`              | `Code`                                 | 한 번 실행되는 do-while 루프 구조 내에서 `Code` 를 실행합니다. 주로 다른 Check에 필요한 정보를 준비할 때 유용합니다. |
| `checkNoEntry`           | (없음)                                   | `check(false)` 와 비슷하게 라인에 히트하면 실행을 정지하지만, 도달할 수 없는 코드 경로에 사용합니다.              |
| `checkNoReentry`         | (없음)                                   | 라인에 두 번 이상 히트하면 실행을 정지합니다.                                                    |
| `checkNoRecursion`       | (없음)                                   | 범위 내에서 라인에 두 번 이상 히트하면 실행을 정지합니다.                                             |
| `unimplemented`          | (없음)                                   | `check(false)` 와 비슷하게 라인에 히트하면 실행을 정지하지만, 오버라이드되어야 하며 호출되지 않는 가상 함수에 사용합니다.   |

[UE_Docs](https://dev.epicgames.com/documentation/unreal-engine/asserts-in-unreal-engine)