Unreal BP에서 편하게 데미지 관련 처리를 하기위해 만들어진 함수들이다.
```cpp title:ApplyDamage_Series
	float UGameplayStatics::ApplyDamage(
	AActor* DamagedActor, //데미지를 받을 액터
	float BaseDamage, // 기본 데미지 수치
	AController* EventInstigator, // 데미지를 주는 사람의 컨트롤러 (킬로그 같은거)
	AActor* DamageCauser, // 실제 유발한 물리적 객체 (총알 등)
	TSubclassOf<UDamageType> DamageTypeClass // 데미지 속성 구분용
	)

float UGameplayStatics::ApplyPointDamage(
	AActor* DamagedActor, //데미지를 받을 액터
	float BaseDamage, // 기본 데미지 수치
	FVector const& HitFromDirection, // 데미지가 들아온 방향 벡터, 어느 방향으로 쓰러지거나 밀려날 때 사용
	FHitResult const& HitInfo, //충돌 정보 구조체,
	AController* EventInstigator, // 데미지를 주는 사람의 컨트롤러 (킬로그 같은거
	AActor* DamageCauser, // 실제 유발한 물리적 객체 (총알 등)
	TSubclassOf<UDamageType> DamageTypeClass)// 데미지 속성 구분용

bool UGameplayStatics::ApplyRadialDamage(
	const UObject* WorldContextObject, // 이 함수가 실행될 월드
	float BaseDamage, // 기본 데미지 수치
	const FVector& Origin, // 중심 위치
	float DamageRadius, // 데미지의 최대 반경
	TSubclassOf<UDamageType> DamageTypeClass,  // 데미지 속성 구분용
	const TArray<AActor*>& IgnoreActors, // 무시할 액터들
	AActor* DamageCauser, // 실제 유발한 물리적 객체 (총알 등)
	AController* InstigatedByController, // 데미지를 주는 사람의 컨트롤러 (킬로그 같은거
	bool bDoFullDamage, // 반경 내에 있다면 온전한 데미지를 줄 것인지, false 시 외각쪽으로 갈수록 0으로 수렵
	ECollisionChannel DamagePreventionChannel 
	)

bool UGameplayStatics::ApplyRadialDamageWithFalloff(
	const UObject* WorldContextObject, // 이 함수가 실행될 월드
	float BaseDamage, // 기본 데미지 수치
	float MinimumDamage, // 바깥 반경에서 받을 최소 데미지
	const FVector& Origin, // 중심 위치
	float DamageInnerRadius, // 내부 반경
	float DamageOuterRadius, // 외부 반경
	float DamageFalloff, // 데미지가 감소하는 곡선의 기울기 (지수)
	TSubclassOf<class UDamageType> DamageTypeClass, // 데미지 속성 구분용
	const TArray<AActor*>& IgnoreActors, // 무시할 액터들
	AActor* DamageCauser, // 실제 유발한 물리적 객체 (총알 등)
	AController* InstigatedByController,  // 데미지를 주는 사람의 컨트롤러 (킬로그 같은거
	ECollisionChannel DamagePreventionChannel // 데미지를 차단할 채널 (사이에 벽이 있으면 차단)
	)
```