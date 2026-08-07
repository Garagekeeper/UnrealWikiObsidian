# SpawnActor\<T\>,SpawnActor()

`SpawnActor<T>`는 UE에서 제공하는 템플릿 함수로 내부에서 `SpawnActor()`를 호출한다. `SpawnActor<T>()`로 액터를 생성한 경우 생성 여부와 상관 없이
반환된 액터의 포인터가 T 타입으로 캐스팅되는지 확인한다. **Casting이 되지 않는경우 Assert가 발생한다!** Spawn을 타고 들어가다보면 실제 메모리를 할당하는 부분을 볼 수 있는데 조건이 맞으면 `PersistentLinearAllocator`라는 클래스를 사용해서 할당을 진행하고 조건이 맞지 않으면 `FMemory::Malloc()`을 통해서 동적 할당을 진행한다. 자세한 내용은 [[Persistent Linear Allocator]]을 살펴보자.

매개변수로 들어간 클래스의 타입 그대로 생성한 것을 T* 로 변환한 것 뿐이다.

```cpp file:World.h hlt:17
/**
	 * Spawn Actors with given transform and SpawnParameters
	 * 
	 * @param	Class					Class to Spawn
	 * @param	Location				Location To Spawn
	 * @param	Rotation				Rotation To Spawn
	 * @param	SpawnParameters			Spawn Parameters
	 *
	 * @return	Actor that just spawned
	 */
	UE_API AActor* SpawnActor( UClass* InClass, FVector const* Location=NULL, FRotator const* Rotation=NULL, const FActorSpawnParameters& SpawnParameters = FActorSpawnParameters() );

	/** Templated version of SpawnActor that allows you to specify a class type via the template type */
	template< class T >
	T* SpawnActor( const FActorSpawnParameters& SpawnParameters = FActorSpawnParameters() )
	{
		return CastChecked<T>(SpawnActor(T::StaticClass(), NULL, NULL, SpawnParameters),ECastCheckedType::NullAllowed);
	}
```

```cpp file:LevelActor.cpp hlt:6
AActor* UWorld::SpawnActor( UClass* Class, FTransform const* UserTransformPtr, const FActorSpawnParameters& SpawnParameters )
{
	...
	
	// actually make the actor object
AActor* const Actor = NewObject<AActor>(LevelToSpawnIn, Class, NewActorName, ActorFlags, Template, false/*bCopyTransientsFromClassDefaults*/, nullptr/*InInstanceGraph*/, ExternalPackage);

	...
}

```

```cpp file:UObjectGlobal.h hlt:25,34,57,62
template< class T >
FUNCTION_NON_NULL_RETURN_START
	T* NewObject(UObject* Outer, const UClass* Class, FName Name = NAME_None, EObjectFlags Flags = RF_NoFlags, UObject* Template = nullptr, bool bCopyTransientsFromClassDefaults = false, FObjectInstancingGraph* InInstanceGraph = nullptr, UPackage* InExternalPackage = nullptr)
FUNCTION_NON_NULL_RETURN_END
{
	if (Name == NAME_None)
	{
		FObjectInitializer::AssertIfInConstructor(Outer, TEXT("NewObject with empty name can't be used to create default subobjects (inside of UObject derived class constructor) as it produces inconsistent object names. Use ObjectInitializer.CreateDefaultSubobject<> instead."));
	}

#if DO_CHECK
	// Class was specified explicitly, so needs to be validated
	CheckIsClassChildOf_Internal(T::StaticClass(), Class);
#endif

	FStaticConstructObjectParameters Params(Class);
	Params.Outer = Outer;
	Params.Name = Name;
	Params.SetFlags = Flags;
	Params.Template = Template;
	Params.bCopyTransientsFromClassDefaults = bCopyTransientsFromClassDefaults;
	Params.InstanceGraph = InInstanceGraph;
	Params.ExternalPackage = InExternalPackage;

	T* Result = static_cast<T*>(StaticConstructObject_Internal(Params));
	return Result;
}

...

UObject* StaticConstructObject_Internal(const FStaticConstructObjectParameters& Params)
{
	...
	Result = StaticAllocateObject(InClass, InOuter, InName, InFlags, Params.InternalSetFlags, bCanRecycleSubobjects, &bRecycledSubobject, Params.ExternalPackage, SerialNumber, RemoteId, &GCGuard);
	...
}


UObject* StaticAllocateObject
UObject* StaticAllocateObject
(
	const UClass*	InClass,
	UObject*		InOuter,
	FName			InName,
	EObjectFlags	InFlags,
	EInternalObjectFlags InternalSetFlags,
	bool bCanRecycleSubobjects,
	bool* bOutRecycledSubobject,
	UPackage* ExternalPackage,
	int32 SerialNumber,
	FRemoteObjectId RemoteId,
	FGCReconstructionGuard* GCGuard)
{
	...
	
	//UObject를 위한 메모리를 할당 받고 이를 UObject타입으로 변환
	Obj = (UObject *)GUObjectAllocator.AllocateUObject(TotalSize,Alignment,GIsInitialLoad);
	
	...
	
	//plcaement new를 통해서 이미 할당된 메모리 위에서 생성자 호출 (여기서 실제 객체 생성)
	new ((void *)Obj) UObjectBase(const_cast<UClass*>(InClass), InFlags|RF_NeedInitialization, InternalSetFlags, InOuter, InName, OldIndex, OldSerialNumber == 0 ? SerialNumber : OldSerialNumber, 
#if UE_WITH_REMOTE_OBJECT_HANDLE
				OldRemoteId.IsValid() ? OldRemoteId :
#endif
				RemoteId);
				
	...
}
```


```cpp file:UObjectAllocator.cpp hlt:16,20,24,25,2
/**
 * 일반적인 동적 할당과 permanent object pool을 통해서 할당을 진행한다.
 * Allocates a UObjectBase from the free store or the permanent object pool
 *
 * @param Size size of uobject to allocate
 * @param Alignment alignment of uobject to allocate
 * @param bAllowPermanent if true, allow allocation in the permanent object pool, if it fits
 * @return newly allocated UObjectBase (not really a UObjectBase yet, no constructor-like thing has been called).
 */
UE_AUTORTFM_ALWAYS_OPEN
UObjectBase* FUObjectAllocator::AllocateUObject(int32 Size, int32 Alignment, bool bAllowPermanent)
{
	void* Result = nullptr;

	// We want to perform this allocation uninstrumented, so the GC can clean this up if the transaction is aborted.
	// 길게 유지되는 메모리는 GetPersistentLinearAllocator에서 매모리를 할당받는다. 주로 (UObject)
	if (bAllowPermanent && !GPersistentAllocatorIsDisabled)
	{
		// This allocation might go over the reserved memory amount and default to FMemory::Malloc, so we are moving it into the AutoRTFM scope.
		Result = GetPersistentLinearAllocator().Allocate(Size, Alignment);
	}
	else
	{
		//일반적인 메모리는 Malloc으로 할당 받는다.
		Result = FMemory::Malloc(Size, Alignment);
	}

	return (UObjectBase*)Result;
}
```