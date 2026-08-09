# Rookiss cpp

## 0307
cpp로 작업하는 방식
- IDE만 딸랑 켜놓고 빌드 후 디버거로 실행
- 기현상 급격히 줄어든다
에디터를 별도로 키고 하는 경우
- ctrl alt f11 눌러서 라이브 코딩
- 기현상이 일어날 확률이 올라감

모듈
- DLL 같은 개념으로 UE 소프트웨어 아키텍처의 가장 기본적인 구성요소
- 모듈 구성해보기
	- 모듈 루트 디렉토리에 이름_Build.cs 생성
	- 종속성 정의
	- 이름_Build.cpp 생성
		-  IMPLEMENT_MODULE(FDefaultModuleImpl, R1Editor);
	- uproject 수정하기
- 모듈의 FdefaultGameModuleImpl을 상속해서 커스텀 가능

로그
- 로그 채널
	- DECLARE_LOG_CATEGORY_EXTERN(LogR2, Log, )
	- DEFINE_LOG_CATEGORY(LogR2);
	- 위 2개 명령어로 카테고리 추가 가능

리플렉션 
- C#
	- 런타임에 엑스레이를 찍는 것
	- Type type = fplayer.GetType();
- UE
	- 리플렉션을 위해서 매크로를 사용
	- 빌드하는 순간 UHT에서 매크로 분석해서 코드 생성해서 등록 준비
	- 변수는 UPROPERTY를 통해서 리플렉션에 등록
		- UFNUNCTION, UCLASS 등등
		- EditAnyWhere, visitAnywhere등 
	- 액터는 월드에 배치되면 루트로 추가됨
일반적인 cpp 형태의 객체 생성 소멸이 아님

GC
- C#
	- 
- Unreal
	- 기존에는 스마트 포인터를 통해서 관리
		- shared, Ref count
	- UE에서는 Mark and Sweep
		- Root에서 도달 가능한 놈들만 남기기
		- 특이한 함수들이 있음
			- AddToRoot 같은거
			
CDO
- UClasss 만들면서 생성됨
- 모든 값들이 기본 값으로 채워진 샘플을 하나 만듬


언리얼에서 매니저의 역할?
- 게임 모드 
	- 게임을 관리하는 아이
	- 오버워치처럼 룰이 많은게임에 좋음
		- 밀기, 점렴, 등
- 게임 인스턴스
	- 엔진에서 사용하기 좋은 전역 싱글톤
	- 게임 시작할 때 딱 하나만
	
## 0312
게임 개발 흐름
- 기획이 나옴
- 원화가 나옴 (캐릭터, 배경) 
- 모델러 (3D asset) (Mesh, 삼각형들의 집합)
	- 스태틱 메시    : 말 그대로 정적인 매시
	- 스켈레탈 메시 : 리깅을 통해서 움직일 수 있도록 만들어줌
- Anim

메시는 삼각형들이 모인것 머터리얼은 메시가 어떤 색상으로 보일것인지

원래는 셰이더로 해야하는데 이러면 사람들 힘드니까 BP로 딸깍 하게 도와주는 것
셰이더에는 정수가 없다 플롯 1~4개

UV매핑
사각형을 가로 세로 0-1 비율로 만들고 텍스쳐를 그 형태에 맞게 알아서 지지고 복고 해서 맞추는거
텍스쳐의 일정 비율을 0-1에 매핑 하는게 UV 매핑

텍스쳐 종류
- N: Normal, 법선 벡터를 통해서 굴곡 표시
- MRA:
- D: Deffuse, 실제로 적용하는 텍스쳐

CreateDefaultSubObject는
Uclass 객체를 TReturnType::StaticClass로 가져옴
여기서 TReturnType::StaticClass는 템플릿으로 런타임에 해당 객체의 정보 가져올 수 있음 내부에서 CDO로 긁어곰

FObjectFinder : CDO를 찾음
FClassFinder   : Uclass 를 찾음

생성자에서는 CDO와 관련한 것 말고는 넣지 말자

액터 스폰시 
GetWorld()->SpawnActor
파괴시 
GetWorld()->DestroyActor(Actor)

setlifeSpan 

`TSubclassOf<AR1Actor> ActorClass;`
-> AR1Actor를 상속받은 무언가를 저장할 수 있는 신기한 아이
내부에 보면 그냥 `TObjectPtr<UClass> Class = nullptr; 이걸 들고있을 뿐인데 복사, 대입 연산자 혹은 생성자 에서 자연스래 캐스팅이 되는 아이들만 넣어진다.

``
```cpp
	/** Construct from a UClass* (or something implicitly convertible to it) */
	template <
		typename U
		UE_REQUIRES( // std::enable_if_t<(__VA_ARGS__)
			!TIsTSubclassOf<std::decay_t<U>>::Value &&
			std::is_convertible_v<U, UClass*>
		)
	>
	[[nodiscard]] UE_FORCEINLINE_HINT TSubclassOf(U&& From)
		: Class(From)
	{
	}
```


TArray를 쓰면서 주의할점 .Empty는 Clear임!

월드 좌표
로컬 좌표

## 0314

Pawn에서는 posses라는 개념이 중요함
![[Rookiss cpp-1786256789080.webp]]