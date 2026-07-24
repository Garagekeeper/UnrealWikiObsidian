# Macro
## 의미
Macro를 언어 그대로 받아들이면 Micro의 반대의미 즉, 거대한 것을 가리킨다. 프로그래밍에서 매크로의 사용 예시를 보면 주로 코드를 줄여주기 위해서 사용된다. 그렇다면 Macro라는 이름이 붙은 이유를 어느 정도 이해할 수 있다. 거대한 코드 덩어리를 묶어놓은 것. 그것이 매크로의 의미가 아닐까 생각해본다.

## Unreal C++의 Macro
요즘에는 언어, 컴파일러의 발달로 매크로를 거의 사용하지 않았던 거 같은데, Unreal cpp 코드를 보고 놀랐다.
액터 하나 생성했는데 벌써 3개의 매크로가 보였다. `UCLASS()`, `UNREAL_CPP_API`, `GENERATED_BODY()`이 매크로들이 기본적으로 생성되고, 변수나 함수를 생성하면 여러 매크로들을 추가 해줘야 한다. 이 매크로들을 대충 기능만 알고 가기엔 아쉬워서 매크로들을 해석하는데 필요한 매크로 들을 알아보자 (AI의 도움을 많이 받았다.) 이 매크로들이 생성되는 과정은 [[UHT(Unreal Header Tool)]]를 알아보자
### [[CURRENT_FILE_ID]]
### [[__LINE__]]

### [[BODY_MACRO_COMBINE(A,B,C,D)]]

### [[PRAGMA_DISABLE_DEPRECATION_WARNINGS]]

### [[PRAGMA_ENABLE_DEPRECATION_WARNINGS]]

### [[DECLARE_FUNCTION]]

### [[DECLARE_CLASS2]]

### [[ProjectName_API]]

### [[check(expreesion)]]
