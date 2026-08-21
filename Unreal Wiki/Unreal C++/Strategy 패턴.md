## 전략 패턴이란?
Strategy(전략) 패턴은 디자인 패턴중 하나로, 런타임에 실행될 알고리즘을 정할 수 있도록 해준다. 객체에 사용될 전용 알고리즘을 만들고 그것을 상황에 맞춰서 수정하는 것이 아니라. 어떤 행동에 대한 유형을 나눠서 알고리즘들을 구현하고 런타임에 어떤 알고리즘을 사용할지 선택하여 알고리즘과 사용자간의 독립성을 유지해준다. 

![[2026.08.21 Unreal C++ 수업-1787303336645.webp]]
Strategy 패턴의 구성요소는 크게 `Client`, `Context`, `StrategyInterface`, `Strategy`의 4가지로 구성된다. `StrategyInterface`에서 어떤 기능을 추상화 시키고 `Strategy`에서 각 기능을 구현한다. `Context` 는 어떤 `Strategy` 실행할지 정하고 실행한다.  `Client`는 `Context`에 알고리즘 선택, 실행등을 요청한다. 실제로 이를 구현한 코드를 살펴보자.

```cpp Title:StrategyPatternInUE hlt:2,27

 // 이 추상 클래스를 기반으로 데이터 에셋별 액션을 구현, Strategy 패턴에서 Strategy 인터페이스와 같은 역할
 // 추상, BP가능, 넣으면서 생성하기(??), 이 클래스를 주소로 가지는 모든 UPROPERTY에 Instanced 속성을 기본적으로 부여(??)
 //Instanced : 객체를 소유자의 하위 인스턴스로 취급(??)(디스크에 저장 가능 + DeepCopy 보장)
UCLASS(Abstract, Blueprintable, EditInlineNew, DefaultToInstanced)
class UNREAL_CPP_API UItemAction : public UObject
{
	GENERATED_BODY()
	
public:
	UFUNCTION(BlueprintNativeEvent, Category = "ItemAction")
	void ExecuteAction(AActor* InInstigator, AActor* InTarget);

	// Abstract 클래스의 경우 BlueprintNativeEvent의 순수 가상함수는 불가능하기에 바디가 필요하다.
	// UHT 의 제약
	virtual void ExecuteAction_Implementation(AActor* InInstigator, AActor* InTarget) {};
	
	
};



...



// 각 전략들을 구현한 파생 클래스
//ItemAction_Money.h
UCLASS()
class UNREAL_CPP_API UItemAction_Money : public UItemAction
{
	GENERATED_BODY()
	
public:
	UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Money");
	int32 Money = 100;

public:
	virtual void ExecuteAction_Implementation(AActor* InInstigator, AActor* InTarget)override;
	
};

//ItemAction_Money.cpp
void UItemAction_Money::ExecuteAction_Implementation(AActor* InInstigator, AActor* InTarget)
{
	if (IInventoryUserInterface* InvenUser = Cast<IInventoryUserInterface>(InTarget))
	{
		FCommandResult res;
		InvenUser->ExecuteInventoryCommand(FInventoryCommand::MakeMoney(Money), res);
	}
	UE_LOG(LogTemp, Display, TEXT("돈 %d 추가하기"), Money);
}

```

```cpp Title:StrategyPatternInUE hlt:1
//전략을 사용하는 클래스 , Strategy 패턴에서의 Context역할
//상황에 맞게 ItemAcion(전략)을 바꿔서 사용할 수 있다.
UCLASS()
class UNREAL_CPP_API UUsableItmeDataAsset : public UMiscItemDataAsset
{
	GENERATED_BODY()
	
public: 
	// UItem의 UPROPERTY 설정으로 인해 에디터 인라인 생성 맟 직렬화 자동 보장
	UPROPERTY(EditAnywhere, Category = "ItemData|Action")
	TObjectPtr<UItemAction> ItemAciton = nullptr;	
};

...

// 실제 사용시 클라이언트는 전략 내부의 코드 수정 없이
// 전략을 변경해서 로직을 변경할 수 있다.
if (Usable->ItemAciton)
{
	// 바꿀일 있으면 메소드 만들어서 바꾸기만 하면도미/
	Usable->ItemAciton->ExecuteAction(GetOwner(), GetOwner());
	UpdateSlotCount(InIndex, -1);
}

```

이런식으로 Unreal Engine에서 전략 패턴을 적용할 수 있다. 여기에 추가로 언리얼에서 전략패턴을 적용시킬때 유용한 기능이 있다. `UCLASS(Abstract, Blueprintable, EditInlineNew, DefaultToInstanced)` 이 매크로를 사용하면 전략 패턴을 더욱 유용하게 사용할 수 있다. 뒤쪽 2개 키워드가 핵심인데 하나씩 살펴보자. 

## 전략 패턴에 도움을 주는 지정자
### [[EditInlineNew]]
### [[DefaultToInstanced]]
