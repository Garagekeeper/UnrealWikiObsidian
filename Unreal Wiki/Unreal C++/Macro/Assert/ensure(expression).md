Assert 매크로, [[verify(expreesion)]]계열과 유사하게 동작하지만, 치명적이지 않은 오류에 사용. 표현식이 false가 된 경우 크래시 리포터에 알리긴 하지만, 실행은 계속됨!


| 매크로                | 파라미터                                 | 동작                                                                      |
| ------------------ | ------------------------------------ | ----------------------------------------------------------------------- |
| `ensure`           | `Expression`                         | `Expression` 이 처음으로 false인 경우 크래시 리포터에 알립니다.                            |
| `ensureMsgf`       | `Expression`, `FormattedText`, `...` | `Expression` 이 처음으로 false인 경우 크래시 리포터에 알리고 로그에 `FormattedText` 를 출력합니다. |
| `ensureAlways`     | `Expression`                         | `Expression` 이 false인 경우 크래시 리포터에 알립니다.                                 |
| `ensureAlwaysMsgf` | `Expression`, `FormattedText`, `...` | `Expression` 이 false인 경우 크래시 리포터에 알리고 로그에 `FormattedText` 를 출력합니다.      |

[UE_Docs](https://dev.epicgames.com/documentation/unreal-engine/asserts-in-unreal-engine)