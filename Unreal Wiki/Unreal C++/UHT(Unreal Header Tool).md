# UHT (Unreal Header Tool)
UHT는 UObject 시스템을 위해서 코드를 파싱/생성하는 툴이다. 활용되는 과정은 다음과 같다.
* UBT(Unreal Build Tool)이 UHT를 부르면 UHT는 헤더 파일을 돌면서 UCLASS() 같이 UObject와 관련한 메타데이터를 파싱하고 이와 관련된 구현 코드/매크로들을 생성해준다.
* 그 뒤 UBT가 C++ 컴파일러를 호출해 실제 컴파일을 진행한다.
![[UPROPERTY-1784628555009.webp|538]]

UHT가 생성한 코드들은 ~.generated.h 에 들어있다. AActor.h 등에서 에서 정의로 이동해서 파일 내용을 볼 수 있고, 사용자가 생성한 Uobject 상속 클래스는 빌드하면 `C:\UnrealCpp\Intermediate\Build\Win64\UnrealEditor\Inc\Unreal_Cpp\UHT\FloatingActor.generated.h` 이런식으로 Intermediate에 생성된다.

![[Pasted image 20260720190328.png|944]]
(실제 생성된 generated.h)

![[Pasted image 20260720194250.png|1532]]
(실제 생성된 gen.cpp일부)