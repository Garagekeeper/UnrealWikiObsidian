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
		- 밀기, 점렴, 등등ㅊㅊㅊ
- 게임 인스턴스
	- 엔진에서 사용하기 좋은 전역 싱글톤
	- 게임 시작할 때 딱 하나만