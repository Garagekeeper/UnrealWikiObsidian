# TShaerdPtr
`std::shared_ptr`를 UE에서 구현한 버전이다. 주의할 점은 `UObject System`에 의해서 관리되는 객체에는 사용할 수 없다.
구현된 코드를 살펴보면 2가지 멤버 변수를 가지고 있다. TSharedPtr이 참조할 대상의 주소를 담을 포인터 변수와 참조카운트를 관리할 변수를 FSharedReferencer타입으로 들고 있다. 여기서 Mode는 ESPMode를 의미하며 Fast와 ThreadSafe가 존재한다.

여태 공부한 바로는 TSharedPtr이 제어블록의 포인터를 들고 있어야하지만 단순히 변수로 들고 있는걸 볼 수 있는데, 이는 FSharedReferencer내부에 제어블록을 가리키는 포인터가 있기 때문이다.
```cpp file:SharedPointer.h hlt:2,8
	/** The object we're holding a reference to.  Can be nullptr. */
	// 참조할 대상
	ObjectType* Object;

	/** Interface to the reference counter for this object.  Note that the actual reference
		controller object is shared by all shared and weak pointers that refer to the object */
	// 참조 카운트를 관리하기 위한 인터페이스
	// 제어 블록
	SharedPointerInternals::FSharedReferencer< Mode > SharedReferenceCount;
```


<br>


아래의 코드를 보면 내부에 제어블록을 위한 포인터를 들고 있고, 제어블록은 참조 횟수(Reference Count)를 관리한다. 참조 횟수는 SharedRef와 WeakRef로 구분되는데, 두가지 횟수는 용도가 다르다. 

먼저 `SharedReferenceCount`의 경우 참조하는 객체의 생명주기에 관여한다. **해당 횟수가 0으로 변하면 참조하던 객체를 파괴한다**. 포인터를 생성할 때 자동으로 1로 세팅된다. 쉐어드 포인터를 생성하는 것 자체가 누군가를 참조하고 있다는 뜻이기 때문이다. 대입/복사 연산자, 생성자 등에서 값을 증가시킨다. `FSharedReferencer`의 소멸자에서 횟수를 감소 시킨다

다음으로 `WeakReferenceCount`이다. 방금 말한이유와 동일하게 1부터 시작한다. 자세한 내용은 [[TWeakPtr]]을 참고하자.



```cpp file:SharedPointerInternals.h hlt:9,18,29,37,47,61
// TSharedPtr
class TSharedPtr
{
	...
	
	private:
		/** Pointer to the reference controller for the object a shared reference/pointer is referencing */
		// 현재 공유되는 포인터 혹은 참조를 위한 레퍼런스 컨트롤러를 가리키는 포인터
		// 제어 블록을 가리키는 포인터
		TReferenceControllerBase<Mode>* ReferenceController;
		
}

// 제어 블록 포인터
template <ESPMode Mode>
class TReferenceControllerBase
{
	// 해당 타입은 ThreadSafe이면 atomic<int32>, Fast면 int32로 설정된다.
	using RefCountType = std::conditional_t<Mode == ESPMode::ThreadSafe, std::atomic<int32>, int32>;

public:
	UE_FORCEINLINE_HINT explicit TReferenceControllerBase() = default;

	// Number of shared references to this object.  When this count reaches zero, the associated object
	// will be destroyed (even if there are still weak references!), but not the reference controller.
	//
	// This starts at 1 because we create reference controllers via the construction of a TSharedPtr,
	// and that is the first reference.  There is no point in starting at 0 and then incrementing it.
	// 타입에 따라서 atomic<int32> 혹은 int32 타입이 된다.
	RefCountType SharedReferenceCount{1};

	// Number of weak references to this object.  If there are any shared references, that counts as one
	// weak reference too.  When this count reaches zero, the reference controller will be deleted.
	//
	// This starts at 1 because it represents the shared reference that we are also initializing
	// SharedReferenceCount with.
	// 타입에 따라서 atomic<int32> 혹은 int32 타입이 된다.
	RefCountType WeakReferenceCount{1};
	
	...
}

class FSharedReferencer
{
	...
	
	// 복사 생성자에서 SharedReference를 증가 시키는 모습
	inline FSharedReferencer( FSharedReferencer const& InSharedReference )
		: ReferenceController( InSharedReference.ReferenceController )
	{
		// If the incoming reference had an object associated with it, then go ahead and increment the
		// shared reference count
		if( ReferenceController != nullptr )
		{
			ReferenceController->AddSharedReference();
		}
	}
	
	
	/** Destructor. */
	// 소멸자에서 SharedReference를 줄이는 모습
	inline ~FSharedReferencer()
	{
		TReferenceControllerBase<Mode>::ReleaseSharedReferenceNoInline(ReferenceController);
	}
	...
}

```


TSharedPtr는 주의할 점이 있는데, 아래 생성자를 보면 알 수 있듯 생성자로 포인터를 넘겨주면 새로운 제어블록을 생성한다. 제어블록이 다른 스마트 포인터는 다른 대상을 가리킨다는 의미이다. 그런데 가끔 this를 통해서 TSharedPtr을 만들어야하는 경우가 있는데, 이런 경우에는  `std::enable_shared_from_this`와 같은 역할을 하는 `TSharedFromThis::SharedThis`를사용하자 (해당 클래스를 상속 받아서 사용!)
```cpp title:주의사항!
	inline explicit TSharedPtr( OtherType* InObject )
		: Object( InObject )
		, SharedReferenceCount( SharedPointerInternals::NewDefaultReferenceController< Mode >( InObject ) )
	{
		// If the object happens to be derived from TSharedFromThis, the following method
		// will prime the object with a weak pointer to itself.
		SharedPointerInternals::EnableSharedFromThis( this, InObject );
	}
```
