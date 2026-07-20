# Macro
## 의미
Macro를 언어 그대로 받아들이면 Micro의 반대의미 즉, 거대한 것을 가리킨다. 프로그래밍에서 매크로의 사용 예시를 보면 주로 코드를 줄여주기 위해서 사용된다. 그렇다면 Macro라는 이름이 붙은 이유를 어느 정도 이해할 수 있다. 거대한 코드 덩어리를 묶어놓은 것. 그것이 매크로의 의미가 아닐까 생각해본다.

## Unreal C++의 Macro
요즘에는 언어, 컴파일러의 발달로 매크로를 거의 사용하지 않았던 거 같은데, Unreal cpp 코드를 보고 놀랐다.
액터 하나 생성했는데 벌써 3개의 매크로가 보였다. `UCLASS()`, `UNREAL_CPP_API`, `GENERATED_BODY()`이 매크로들이 기본적으로 생성되고, 변수나 함수를 생성하면 여러 매크로들을 추가 해줘야 한다. 이 매크로들을 대충 기능만 알고 가기엔 아쉬워서 매크로들을 해석하는데 필요한 매크로 들을 알아보자 (AI의 도움을 많이 받았다.) 이 매크로들이 생성되는 과정은 [[UHT(Unreal Header Tool)]]를 알아보자

### CURRENT_FILE_ID
UHT에서 생성해주는 매크로로 주로 파일 경로가 들어간다.
`CURRENT_FILE_ID FID_UEProjects_UnrealCpp_Source_Unreal_Cpp_Public_FloatingActor_h`

### \____LINE\__
현재 이 매크로가 작성된 라인 수를 정수 값으로 반환

### BODY_MACRO_COMBINE(A,B,C,D)
ABCD를 이어 하나의 매크로로 만들어준다. 내부에서 BODY_MACRO_COMBINE_INNER를 호출해 A,B,C,D에 매크로가 있어도 변환 후 결합된다.

### PRAGMA_DISABLE_DEPRECATION_WARNINGS
지원되지 않는 함수 등에 대한 경고 끄기

### PRAGMA_ENABLE_DEPRECATION_WARNINGS
지원되지 않는 함수 등에 대한 경고 켜기

### DECLARE_FUNCTION
말 그대로 함수를 선언하는 매크로 
`#define DECLARE_FUNCTION(func) static void func( UObject* Context, FFrame& Stack, RESULT_DECL )`

### DECLARE_CLASS2
클래스를 선언하는 매크로

![[Pasted image 20260720193225.png]]

### "Project_Name"\_API
`UNREAL_CPP_API` 형태로 사용되며 외부 모듈의 가시성을 제어하는 매크로 본인이 사용하면 공개
다른 곳에서 사용하면 가져와서 쓰는 것