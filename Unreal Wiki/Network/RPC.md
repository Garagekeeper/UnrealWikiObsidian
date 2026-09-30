# RPC (Remote Procedure Call)
RPC는 네트워크를 통해 다른 머신에서 함수를 실행하는 기능이다. 일반적인 **프로퍼티 복제는 서버에서 클라이언트로** 이루어지지만, RPC를 사용하면 클라이언트가 서버에 동작을 요청하거나 서버가 클라이언트에 특정 동작을 실행하도록 지시할 수 있다. 
UDP기반 통신이기 때문에 신뢰성을 보장받지 못한다 (도착, 순서). 하지만 `Reliable` 키워드를 통해서 신뢰성을 보장 받을 수 있다.

- `ServerRPC` 
	- 서버에서 실행되는 함수
	- 몬스터 피격등 서버의 판정이 필요한 동작들에 사용
	- 자신이 소유한(Connection이 있는) 액터를 통해서만 호출가능!
	- 매크로를 통해서 사용 가능`UFUNCTION(Server, Reliable, WithValidation)`
		- 앞에 `Server_` 접두사를 붙이는게 일반적
		- `WithValidation`을 사용하는 경우 `_Validate` 를 붙여서 bool을 반환하는 검증함수를 만들 수 있음
			- **false 반환시 클라이언트 연결 해제됨!**
			- 일반적인 게임 조건을 넣지 말 것
			- 공정성을 해칠 수 있는 것들을 넣으세요
		- 실제 구현은 `_Implementation` 접미사를 붙여서 구현
		  
- `ClientRPC`
	- 클라이언트에서 실행되는 함수
	- 운영자 귓말 같이 특정 클라이언트에만 필요한 동작에 사용
	- 매크로를 통해서 사용가능 `UFUNCTION(Client, Reliable)`
		- 실제 구현은 `_Implementation` 접미사를 붙여서 구현
		
- `MulticastRPC`
	- 서버/클라 모두 다 실행하는 함수
	- RPG에서 최초 토벌 알림같이 모든 인원에게 전파해야할 때
	- 매크로를 통해서 사용가능 `UFUNCTION(NetMultiCast, Reliable)`
		- 실제 구현은 `_Implementation` 접미사를 붙여서 구현


```cpp Title:ServerRPC_Example

void SetMyPlayerName(const FString& InNewName);

UFUNCTION(Server, Reliable, WithValidation)
	void Server_SetMyPlayerName(const FString& InNewName);
	
	
//------------------------------------------------------------

void ATestPlayerState::SetMyPlayerName(const FString& InNewName)
{
	if (!HasAuthority())
	{
		Server_SetMyPlayerName(InNewName);
		return;
	}

	if (InNewName.IsEmpty())
	{
		MyPlayerName = TEXT("플레이어");
	}
	else
	{
		MyPlayerName = InNewName;
	}

	OnRepNotify_MyPlayerName();
}

// 실제 구현
void ATestPlayerState::Server_SetMyPlayerName_Implementation(const FString& InNewName)
{
	SetMyPlayerName(InNewName);
}

// 검증용 함수 true를 반환하면 Server_SetMyPlayerName실행
bool ATestPlayerState::Server_SetMyPlayerName_Validate(const FString& InNewName)
{
	return InNewName.Len() <= 10;
}
```


</br>


```cpp Title:ClientRPC_Example

UFUNCTION(Client, UnReliable)
	void Client_OnHit();
	
	
//------------------------------------------------------------

void ANetTestCharacter03_RPC::Client_OnHit_Implementation()
{
	//로직
}
```


</br>


```cpp Title:ClientRPC_Example

UFUNCTION(NetMulticast, Unreliable)
	void MultiCast_HitEffect(const FVector& InLcoation, const FRotator& InRotator);
	
	
//------------------------------------------------------------

void ANetProjectile::MultiCast_HitEffect_Implementation(const FVector & InLcoation, const FRotator& InRotator)
{
	//로직
}
```