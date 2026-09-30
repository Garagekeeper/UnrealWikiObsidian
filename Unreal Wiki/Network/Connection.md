언리얼 네트워크와 멀티 플레이에서 클라이언트가 서버에 접속하면 [[UNetDriver]]를 통해서 전용 통신 회선 Net Connection이 양쪽에 생성된다 이 Connection을 통해서 [[RPC]]를 요청하고 [[Replication]] 데이터를 주고 받는다. 여기서 생성된 Net Connection은 PlayerController와 연결되어 각 클라이언트를 식별하며, 액터의 소유권과 RPC권한을 판단하는 기준으로 사용된다.

 - 클라이언트가 서버에 접속하여 `UNetConnection`이 생성되면 서버는 서버내에서 해당 클라이언트를 담당할 PlayerController를 생성한다. 
	 - [[UNetDriver]]를 통해서`UNetConnection`을 생성하면 서버의 `UNetConnection`은 ClientConnection (여러개 가질 수 있음) 클라이언트의 `UNetConnection`은 ServerConnection(하나만 있음) 으로 [[UNetDriver]]에 저장
 - 서버의 PlayerController의 Player멤버는 `UNetConnection`을 참조
 - `UNetConnection` 또한 `OwningActor`등을 통해서 자신과 연결된 PlayerController를 참조
 - 클라이언트 PlayerController의 Player 멤버는 `ULocalPlayer` 를 참조. 서버로 향하는 ServerConnection으로 관리됨.
 - 이러한 구조를 가지기 때문에 플레이어 컨트롤러와 연결된 (해당 커넥션이 소유한) 액터/폰에 대해서 [[Replication]]과 [[RPC]]를 적용할 수 있음
	 - 조금 더 자세히 말하자면 Actor의 Owner 체인을 따라 올라갔을 때 PlayerController에 도달하면 해당 PlayerController와 연결된 Connection(Owning Connection)을 찾을 수 있고 이를 기준으로 RPC, Replication의 여러 조건 및 판단에 사용
 - Connection을 통해서 연결을 하는것은 알겠는데 해당 데이터가 어떤 액터에 관한 것인지 어떻게 알지요?
	 - Connection 내부의 [[UActorChannel]]을 통해서 구분
 
```cpp file:APlayerController.h hlt:10,12
class APlayerController : public AController, public IWorldPartitionStreamingSourceProvider
{
	GENERATED_BODY()

public:
	/** Default Constructor */
	ENGINE_API APlayerController(const FObjectInitializer& ObjectInitializer = FObjectInitializer::Get());

	/** UPlayer associated with this PlayerController.  Could be a local player or a net connection. */
	// 이 변수에 NetConnection혹은 로컬 플레이어를 저장
	UPROPERTY()
	TObjectPtr<UPlayer> Player;
}
```

하지만 월드에 있는 모든 액터의 정보를 전송하는 건 서버의 CPU와 네트워크에 부담이 될 수 있다. Unreal은 [[Actor Relevancy]]을 통해서 문제를 해결. 간단히 말하면 모든 액터의 정보를 네트워크로 전송하는 것이 아니라. 관련있는 액터의 정보만 갱신 하는 것. 가장 간단한 예시로는 가까운 액터만 갱신하는 방법 등 여러 옵션이 존재한다.