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

Pawn이나 Character에서 SetPlayerInputComponent를 사용해도 좋지만 조금 더 깔끔한 방법은 **플레이어 컨트롤러에서 입력 바인딩**을 하는게 좋다
Pawn에서는 posses라는 개념이 중요함
![[Rookiss cpp-1786256789080.webp|541x214]]


![[Rookiss cpp-1786257417994.webp|331x189]]
```csharp
// 헤더파일을 찾을 때 R1폴더를 기준으로 다시 찾아본다.
PublicIncludePaths.AddRange(new String[]
{
	"R1"
});
```

BuildString 말고 좋은 방법인듯
![[Rookiss cpp-1786273695690.webp|772x355]]


AddMovementInput()의 의미
- Pawn에다가 추가
- 움직이겠다는게 아니라, 움직일 방향을 입력하겟다!
- 이것만으로는 폰이 움직이지 않음

MovementComponent
- AddMovementInput으로 추가된 입력들을 처리해서 실제 캐릭터를 이동시키는 컴포넌트
- 내부에서 값을 조정해서 대각선 문제를 해결한다. 
- 정규화를 통해서 해결하는구나 생각하기 쉽지만 내부에서 Clamp를 통해 최댓값을 1로 제한하는 방식
	- 내부에서 1/root(size)를 [[SSE Intrinsic]](간단히 말하면 어셈블리와 가장 가까운 C 명령어)으로 빠르게 계산
- 따라서 여기에 넣기전에 굳이 정규화를 안해줘도 된다.

AddYawInput()
- PlayerController에 추가
- 왜 여기다가 넣었을까
	- 고정 시점이 아닌 경우에 w를 누르면 카메라의 앞쪽으로 가야할까, 플레이어의 앞쪽으로 가야할까?
	- 이런 고민들을 해결하기 위해서 UE는 컨트롤러에 회전 입력을 기록해놓고 원하는대로 처리
- GetControlRoation을 통해서 기록된 회전값을 가져오는 것이다.
- 그러니까 usecontrolleryaw하면 기록된 회전값으로 pawn이 회전하는 것

CharacterMovementComponent
- 이 컴포넌트는 오직 캐릭터에만 붙어서 작동한다. 폰에 붙으면 작동 안함
- 조금 더 디테일한 회전 조절 가능
	- 움직이지 않을 때 마우스를 돌리면 가만히 있지만, 움직일때는 마우스를 따라가게
		![[Rookiss cpp-1786282418360.webp]]



## 0319
GamePlayTage는 계층적인 Enum으로 생각하면 편하고, GAS에 사용된다
`UE_DECLARE_GAMEPLAY_TAG_EXTERN`와 `UE_DEFINE_GAMEPLAY_TAG`는 한 쌍이다

```cpp file:R1gameplayTags.h

#pragma once

#include "NativeGamePlayTags.h"

namespace R1GameplayTags
{
	UE_DECLARE_GAMEPLAY_TAG_EXTERN(Input_Action_Move);
	UE_DECLARE_GAMEPLAY_TAG_EXTERN(Input_Action_Turn);
}

```

```cpp file:R1gameplayTags.cpp

#include "R1GamePlayTags.h"

namespace R1GameplayTags
{
	UE_DEFINE_GAMEPLAY_TAG(Input_Action_Move, "Input.Action.Move");
	UE_DEFINE_GAMEPLAY_TAG(Input_Action_Turn, "Input.Action.Turn");
}
```


<br>


unreal은 Asset을관리하는 AssetManager가 이미 있음 이걸 오버라이드해서 입맛대로 쓰자
싱글톤 형태로 관리하고 프로젝트 세팅에서 변경할 수 있음

```cpp file:UR1AssetManager.h
#pragma once

#include "CoreMinimal.h"
#include "Engine/AssetManager.h"
#include "R1AssetManager.generated.h"

/**
 * 
 */
UCLASS()
class R1_2_API UR1AssetManager : public UAssetManager
{
	GENERATED_BODY()
	

public:
	UR1AssetManager();

	static UR1AssetManager& Get();
};


```

```cpp file:UR1AssetManager.cpp
#include "System/R1AssetManager.h"

UR1AssetManager::UR1AssetManager() : Super()
{

}

UR1AssetManager& UR1AssetManager::Get()
{
	if (UR1AssetManager* Singletone = Cast<UR1AssetManager>(GEngine->AssetManager))
	{
		return *Singletone;
	}

	UE_LOG(LogTemp, Fatal, TEXT("Cannot find UR1Assetmanger"));

	return *NewObject<UR1AssetManager>();
}
```

데이터 에셋 정의하기

```cpp file:UR1InputDataAsset.h


#pragma once

#include "GameplayTagContainer.h"
#include "Engine/DataAsset.h"
#include "R1InputDataAsset.generated.h"

class UInputAction;
class UInputMappingContext;


USTRUCT()
struct FR1InputAction
{
	GENERATED_BODY()

public:
	UPROPERTY(EditDefaultsOnly)
	FGameplayTag InputTag = FGameplayTag::EmptyTag;

	UPROPERTY(EditDefaultsOnly)
	TObjectPtr<UInputAction> InputAction = nullptr;
};

/**
 * 
 */
UCLASS()
class R1_2_API UR1InputDataAsset : public UDataAsset
{
	GENERATED_BODY()
	
	// 태그를 통해서 IA가져오기
	const UInputAction* FindInputActionByTag(const FGameplayTag& InputTag) const;

public:
	UPROPERTY(EditDefaultsOnly)
	TObjectPtr<UInputMappingContext> InputMappingContext;

	UPROPERTY(EditDefaultsOnly)
	TArray<FR1InputAction> InputActions;

};

```

```cpp file:UR1InputDataAsset.cpp
#include "Data/R1InputDataAsset.h"

const UInputAction* UR1InputDataAsset::FindInputActionByTag(const FGameplayTag& InputTag) const
{
	for (const FR1InputAction& Action : InputActions)
	{
		if (Action.InputAction && Action.InputTag == InputTag)
		{
			return Action.InputAction;
		}
	}

	UE_LOG(LogTemp, Error, TEXT("Cannot find InputAction for InputTag [%s]"), *InputTag.ToString());

	return nullptr;
}

```


```cpp file:UR1AssetData.h


#pragma once

#include "CoreMinimal.h"
#include "Engine/DataAsset.h"
#include "R1AssetData.generated.h"


// 에셋의 묶음
USTRUCT()
struct FAssetEntry
{
	GENERATED_BODY()

public:
	// 에셋 이름
	UPROPERTY(EditDefaultsOnly)
	FName AssetName;

	// 에셋 경로
	UPROPERTY(EditDefaultsOnly)
	FSoftObjectPath AssetPath;

	// 에셋 레이블
	UPROPERTY(EditDefaultsOnly)
	TArray<FName> AssetLabels;
};

// 에셋 묶음을 들고있는 구조체
USTRUCT()
struct FAssetSet
{
	GENERATED_BODY()

public:
	UPROPERTY(EditDefaultsOnly)
	TArray<FAssetEntry> AssetEntries;
};

/**
 *
 */
UCLASS()
class R1_2_API UR1AssetData : public UPrimaryDataAsset
{
	GENERATED_BODY()

public:
	//에디터에서 에셋 저장, 빌드등의 상황에서 자동 호출
	virtual void PreSave(FObjectPreSaveContext ObjectSaveContext) override;

public:
	//에셋 이름을 기반으로 가져오기
	FSoftObjectPath GetAssetPathByName(const FName& AssetName);
	//에셋 레이블을 기반으로 가져오ㄱ;
	const FAssetSet& GetAssetPathByLable(const FName& Lable);

private:
	UPROPERTY(EditDefaultsOnly)
	TMap<FName, FAssetSet> AssetGroupNameToSet;

	UPROPERTY()
	TMap<FName, FSoftObjectPath> AssetNameToPath;

	UPROPERTY()
	TMap<FName, FAssetSet> AssetLabeToSet;
};

```

```cpp file:UR1AssetData.cpp
#include "Data/R1AssetData.h"
#include "UObject/ObjectSaveContext.h"

void UR1AssetData::PreSave(FObjectPreSaveContext ObjectSaveContext)
{
    Super::PreSave(ObjectSaveContext);


    //캐시 비우기
    AssetNameToPath.Empty();
    AssetLabeToSet.Empty();

    // 키정렬...?
    AssetGroupNameToSet.KeySort([](const FName& A, const FName& B)
        {
            return (A.Compare(B) < 0);
        });

    // 에셋 묶음을 담은 맵을 순회
    for (const auto& [Key, Value] : AssetGroupNameToSet)
    {
        // 하나의 에셋 묶음에 대하여
        const FAssetSet& AssetSet = Value;
        // 내부의 엔트리를 순회
        for (FAssetEntry AssetEntry : AssetSet.AssetEntries)
        {
            FSoftObjectPath& AssetPath = AssetEntry.AssetPath;
            const FString& AssetName = AssetPath.GetAssetName();

            // 에셋이 BP계열이면 끝에 _C를 붙여준다 (지금은 잘 몰름)
            if (AssetName.StartsWith(TEXT("BP_")) || AssetName.StartsWith(TEXT("B_")) ||
                AssetName.StartsWith(TEXT("GE_")) || AssetName.StartsWith(TEXT("GA_")))
            {
                FString AssetPathString = AssetPath.GetAssetPathString();
                AssetPathString.Append(TEXT("_C"));
                AssetPath = FSoftObjectPath(AssetPathString);
            }

            // 경로를 기반으로 에셋 경로 저장
            AssetNameToPath.Emplace(AssetEntry.AssetName, AssetEntry.AssetPath);

            // 라벨을 기반으로 에셋 묶음 저장
            for (const FName& Label : AssetEntry.AssetLabels)
            {
                AssetLabeToSet.FindOrAdd(Label).AssetEntries.Emplace(AssetEntry);
            }
        }
    }
}

FSoftObjectPath UR1AssetData::GetAssetPathByName(const FName& AssetName)
{
    FSoftObjectPath* AssetPath = AssetNameToPath.Find(AssetName);
    ensureAlwaysMsgf(AssetPath, TEXT("Can't find asset path from asset name [%s]."), *AssetName.ToString());
    return *AssetPath;
}

const FAssetSet& UR1AssetData::GetAssetPathByLable(const FName& Lable)
{
    const FAssetSet* AssetSet = AssetLabeToSet.Find(Lable);
    ensureAlwaysMsgf(AssetSet, TEXT("Can't find asset path from asset label [%s]."), *Lable.ToString());
    return *AssetSet;
}

```

![[Rookiss cpp-1786378346402.webp]]


![[Rookiss cpp-1786379462372.webp]]

DataAsset vs PrimaryDataAsset
- DataAsset
- PrimaryDataAsset
	- GetPrimaryAssetID를 구현했기 때문에 에셋 번들 지원이 된다
	- 에셋 매니저를 통해서 수동 로딩 언로딩이 된다

데이터 에셋을 만들었으면 자동으로 스캔하도록 설정해주자. 자동으로 스캔한다는 의미가 로딩이 된다는것이 아니고, AssetManager를 통해서 사용이 가능하다는 뜻
![[Rookiss cpp-1786379934852.webp]]

자 이렇게 세팅이 완료되면 교통 정리를 해줘야하는데,
AssetManager는 싱글톤이니까 이걸 통해서 PDA를 접근해야하고, AssetManager는  PDA를 싹다 로딩해서 들고있을 예정이다.
그럼 언제로딩? -> GameInstance 생성될 때


```cpp file:Gameinstance.h

#pragma once

#include "CoreMinimal.h"
#include "Engine/GameInstance.h"
#include "R1GameInstance.generated.h"

/**
 * 
 */
UCLASS()
class R1_2_API UR1GameInstance : public UGameInstance
{
	GENERATED_BODY()	

public:
	UR1GameInstance(const FObjectInitializer& ObjectInitalizer);
	
public:
	virtual void Init() override;
	virtual void Shutdown() override;
};
```

```cpp file:Gameinstance.cpp



#include "System/R1GameInstance.h"
#include "System/R1AssetManager.h"

UR1GameInstance::UR1GameInstance(const FObjectInitializer& ObjectInitalizer)
	:Super(ObjectInitalizer)
{

}

void UR1GameInstance::Init()
{
	Super::Init();

	//Game Instance 생성시에 에셋 매니저 초기화 호출
	UR1AssetManager::Initialize();
}

void UR1GameInstance::Shutdown()
{
	Super::Shutdown();
}

```

```cpp file:R1AssetManager.h

#pragma once

#include "CoreMinimal.h"
#include "Engine/AssetManager.h"
#include "R1AssetManager.generated.h"

/**
 * 
 */
UCLASS()
class R1_2_API UR1AssetManager : public UAssetManager
{
	GENERATED_BODY()
	

public:
	UR1AssetManager();

	static UR1AssetManager& Get();

public:
	static void Initialize();
	
	//TODO AssetLoad
};
```

```cpp file:R1AssetManager.cpp



#include "System/R1AssetManager.h"

UR1AssetManager::UR1AssetManager() : Super()
{

}

UR1AssetManager& UR1AssetManager::Get()
{
	if (UR1AssetManager* Singletone = Cast<UR1AssetManager>(GEngine->AssetManager))
	{
		return *Singletone;
	}

	UE_LOG(LogTemp, Fatal, TEXT("Cannot find UR1Assetmanger"));

	return *NewObject<UR1AssetManager>();
}

void UR1AssetManager::Initialize()
{
	//TODO Asset Load
}

```