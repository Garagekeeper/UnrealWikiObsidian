# Actor Relevancy
네트워크 최적화를 위해 나타난 것으로 액터와 관련이 있는지를 추상화한 개념이다. 넓은 레벨의 수많은 액터들을 전부 복제한다면 서버의 CPU와 네트워크대역폭을 많이 점유한다. UE에서는 이를 해결하기 위해 액터간의 관련성을 정의하고 기본으로 제공되는 Relevancy는 다음과 같다.
- 소유권이 있는 액터 (내가 소유한 액터면 관련이 있다.)
- 소유자의 Relevancy를 그대로 따라감 (Weapon같은 경우가 예시)
- 소유/빙의 상태인 액터 (내가 조종하는 폰, 캐릭터 등)
- 거리기반 컬링 (말 그대로 멀리있는거 복제 안함)

Relevancy가 없다고 판정되면 클라이언트 에서는 Destroy, Relevancy가 없던 액터가 Relevant 판정을 받으면 새로운 액터 인스턴스를 스폰.
사용자 정의 Relevancy도 추가 가능하다. 

```cpp file:Relevancy_Example
bool AMyCharacter::IsNetRelevantFor(const AActor* RealViewer, const AActor* ViewTarget, const FVector& SrcLocation) const 
{   // 1. 기본 거리 및 플래그 검사 
	if (!Super::IsNetRelevantFor(RealViewer, ViewTarget, SrcLocation)) 
	{ 
		return false; 
	} 
	// 2. 은신 상태일 때 아군에게만 보이도록 커스텀 조건 추가 
	if (bIsStealthed) 
	{ 
		const AMyPlayerState* ViewerPS = Cast<AMyPlayerState>(RealViewer); 
		return ViewerPS && (ViewerPS->GetTeamID() == this->GetTeamID()); 
	}
	return true; 
}
```

![[Actor Relevancy-1790761934857.webp]]
![[Actor Relevancy-1790761948451.webp]]

# Dormancy
아무리 Relevancy를 따져도 그냥 좁은 공간에 액터가 많다면? 서버는 오늘도 힘들다. 여기서 최적화 기법이 하나 더 등장한다.  **휴면(Dormancy)처리**가 그것인데. Dormancy는 Relevancy가 없는 **액터를 파괴하지 않고 레플리케이션만 중지(메모리에는 올라감)한다.** 이 기법을 움직임이 많지 않은 (변화가 적은) 액터에 적용한다면? 서버는 아주 기분이 좋은
- `DORM_Never`
- `DORM_Awake`
- `DORM_DormantAll`
- `DORM_Initial`

반드시 잠자는 액터를 `FlushNetDormancy` 혹은 `ForceNetUser`을 사용해서 깨운뒤에 값을 수정해야함!