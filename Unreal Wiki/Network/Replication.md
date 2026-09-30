Replication, 직역하면 복제라는 뜻인데. UE에서도 이 의미 그대로 사용한다. 간단히 말하면 서버의 객체(액터)를 클라이언트들로 복제해주는 기능이다. 전통적인 서버-클라이언트 구조에서는 패킷을 만들고, 보내고, 패킷을 뜯고 적절한 로직을 실행해야 하지만 Replication을 사용하면 딸깍으로 이 과정을 생략할 수 있다.

Replication을 사용하면 **서버에서 클라이언트로** 액터를 복제하고 생명 주기를 공유한다. 액터가 복제되었다고 Deep copy처럼 모든것을 복제해주지는 않는다. 내부의 변수나 컴포넌트등은 별도의 설정을 통해서 복제가 가능하다.

변수의 경우 `Replicated` 나 `ReplicatedUsing`을 사용해서 Replication을 사용할 수 있다.

```cpp Title:변수의Replication

/*---------------------------------
			|Header
------------------------------------*/

protected:
	// 이 함수에서 변수 레플리케이션 설정
	virtual void GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const override;

	UFUNCTION()
	void OnRepNotify_Health();
	
protected:
	// 변수의 데이터를 수신하면 OnRepNotify_Health 실행
	UPROPERTY(VisibleAnywhere, Category = "Test", ReplicatedUsing = OnRepNotify_Health)
	float Health = 100.0f;
	
	// 변수의 데이터를 복제 받음
	UPROPERTY(VisibleAnywhere, Category = "Test", Replicated)
	float Exp = 0.0f;
	
	'''
	
/*---------------------------------
			|cpp
------------------------------------*/

AANetTestCharacter02_Replication::AANetTestCharacter02_Replication()
{
	PrimaryActorTick.bCanEverTick = true;
	bReplicates = true;
	
	OverheadWidgetComp = CreateDefaultSubobject<UWidgetComponent>(TEXT("OverHeadWidgetComp"));
	OverheadWidgetComp->SetupAttachment(RootComponent);
}

void AANetTestCharacter02_Replication::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	// 이 매크로를 통해서 조건부 레플리케이션을 할 수 있다.
	DOREPLIFETIME_CONDITION(AANetTestCharacter02_Replication, Level, COND_OwnerOnly);	// 오너에게만 리플리케이션 한다.
	//DOREPLIFETIME(AANetTestCharacter02_Replication, Level);	// 모두에게 리플리케이션
	DOREPLIFETIME_CONDITION(AANetTestCharacter02_Replication, Exp, COND_SimulatedOnly);	// SimProxy에만 리플리케이션, 
	
	// 조건 없는 레플리케이션
	DOREPLIFETIME(AANetTestCharacter02_Replication, Health);
}

void AANetTestCharacter02_Replication::OnRepNotify_Health()
{
	//const FString Str = FString::Printf(TEXT("서버에서 체력을 %.1f로 변경했다고 알리고 있습니다."), Health);
	//GEngine->AddOnScreenDebugMessage(-1, 3.0f, FColor::Yellow, Str);
	OnHealthChanged.Broadcast(Health);
}
	
```

컴포넌트의 경우 `Component Replicates`옵션을 통해서 레플리케이션을 할 수 있다.(소유한 액터가 Replication상태일때만) 역시나 내부 변수는 위의 방법으로 복제해야한다.