의미 그대로 네트워크 상에서 해당 머신의 액터가 어떤 역할인지 나타내는 Enum이다. 이를 통해서 서버/클라이언트, 클라이언트/클라이언트 간의 구분을 할 수 있다.

![[NetRole-1790759863359.webp]]

- `ROLE_Authority` : 서버
- `ROLE_AutonomousProxy` : 내가 조종하는 프록시(복제품, 클라이언트)
- `ROLE_SimulatedProxy` : 남이 조종하는 프록시(복제품, 클라이언트)

`GetLocalRole()` : 현재 머신에서 액터의 역할이 무엇인지
`GetRemoteRole()` : 나의 반대편(연결된) 에서는 어떤 역할인지

![[NetRole-1790759886834.webp]]
