# TWeakPtr
`std::weak_ptr`을 UE에서 구현한 버전이다. 주의할 점은 `UObject System`에 의해서 관리되는 객체에는 사용할 수 없다.
구현된 코드를 살펴보면 2가지 멤버 변수를 가지고 있다. TWeakPtr 참조할 대상의 주소를 담을 포인터 변수와 참조카운트를 관리할 변수를 FSharedReferencer타입으로 들고 있다. 여기서 Mode는 ESPMode를 의미하며 Fast와 ThreadSafe가 존재한다.

여태 공부한 바로는 TWeakPtr 제어블록의 포인터를 들고 있어야하지만 단순히 변수로 들고 있는걸 볼 수 있는데, 이는 FWeakReferencer 내부에 제어블록을 가리키는 포인터가 있기 때문이다.

```cpp file:SharedPointer.h hlt:
class TWeakPtr
{
private:
	/** The object we have a weak reference to.  Can be nullptr.  Also, it's important to note that because
	    this is a weak reference, the object this pointer points to may have already been destroyed. */
	// 참조할 대상을 가리키는 포인터 (이미 파괴되었을 수 있음)
	ObjectType* Object;

	/** Interface to the reference counter for this object.  Note that the actual reference
		controller object is shared by all shared and weak pointers that refer to the object */
	// 제어블록을 위한 변수
	SharedPointerInternals::FWeakReferencer< Mode > WeakReferenceCount;
}
```

아래의 코드를 보면 내부에 제어블록을 위한 포인터를 들고 있고, 제어블록은 참조 횟수(Reference Count)를 관리한다. 참조 횟수는 SharedRef와 WeakRef로 구분되는데, 두가지 횟수는 용도가 다르다. 

먼저 `SharedReferenceCount`의 [[TSharedPtr]]을 참고하자.

다음으로 `WeakReferenceCount`이다. 포인터를 생성하는 것 자체가 누군가를 참조하고 있다는 뜻이기 때문에 1부터 시작한다.
조금 특이한 특성을 가지는데. TWeakPtr를 통해서 참조를 하면 `WeakReferenceCount`가 증가하고, TSharedPtr을 통해서 참조하는 경우 또한 `WeakReferenceCount`가 증가한다. 
하지만 이 횟수는 **객체의 수명에 영향을 끼치지 않는다**. `SharedReferenceCount`가 0으로 변하면  **`WeakReferenceCount`의 값에 상관없이 `SharedReferenceCount`가 0이되면 참조하고 있는 객체를 파괴한다**. 

그렇다면 객체의 소멸을 막지 않는 이 카운트는 왜 사용할까? 객체의 소멸을 막지 않기 때문에 TSharedPtr의 순환 참조 문제를 해결하기 위해 사용된다. 하지만 이것이 `WeakReferenceCount`를 관리하는 이유는 아니다. `WeakReferenceCount`는 **제어블록의 수명**을 관리하는데 사용된다. 해당 값이 0이되면 제어블록 자체가 사라진다. 제어블록을 유지하면 객체를 안전하게 확인할 수 있다. 객체가 살아 있는지 확인 해야하는데, 제어 블록이 없다면 해당 객체에 접근하는 행위 자체가 위험하다. 하지만 weak을 통해서 제어 블록을 유지하면 `SharedReferenceCount`의 값만 보고 해당 객체가 살아있는지 아닌지 알 수 있다.

`Pin`을 통해서 해당 TWeakPtr을 TSharedPtr로 변환 시킬 수 있고, `IsValid`를 통해 객체가 유효한지(메모리에 있는지) 확인할 수 있다

```cpp file:SharedPointerInternals.h hlt:8,35

// 제어블록 클래스
class TRederenceControllerBase
{
		...
		
		/** Releases a weak reference to this counter */
		// 약한 참조 횟수를 감소 시킨다.
		void ReleaseWeakReference()
		{
			if constexpr (Mode == ESPMode::ThreadSafe)
			{
				UE_AUTORTFM_ONCOMMIT(this)
					{
						// See ReleaseSharedReference for the same reasons that std::memory_order_acq_rel is used in this function.

						int32 OldWeakCount = WeakReferenceCount.fetch_sub(1, std::memory_order_acq_rel);
						checkSlow(OldWeakCount > 0);
						if (OldWeakCount == 1)
						{
							// Disable this if running clang's static analyzer. Passing shared pointers
							// and references to functions it cannot reason about, produces false
							// positives about use-after-free in the TSharedPtr/TSharedRef destructors.
#if !defined(__clang_analyzer__)
							// No more references to this reference count.  Destroy it!
							delete this;
#endif
						}
					};
			}
			else
			{
				checkSlow( WeakReferenceCount > 0 );

				// 약한 참조 횟수가 0이되면 제어 블록 자체를 삭제한다.
				if( --WeakReferenceCount == 0 )
				{
					// No more references to this reference count.  Destroy it!
#if !defined(__clang_analyzer__)
					delete this;
#endif
				}
			}
		}
		
		...
}
```

```cpp file:SharedPointer.h hlt:12,25
class TWeakPtr
{
	...
	/**
	 * Converts this weak pointer to a shared pointer that you can use to access the object (if it
	 * hasn't expired yet.)  Remember, if there are no more shared references to the object, the
	 * returned shared pointer will not be valid.  You should always check to make sure the returned
	 * pointer is valid before trying to dereference the shared pointer!
	 *
	 * @return  Shared pointer for this object (will only be valid if still referenced!)
	 */
	 // TWeakPtr의 멤버 함수로, TWeakPtr을 TSharedPtr로 변환시킨다
	[[nodiscard]] UE_FORCEINLINE_HINT TSharedPtr< ObjectType, Mode > Pin() &&
	{
		return TSharedPtr< ObjectType, Mode >( MoveTemp( *this ) );
	}
	...
	
	/**
	 * Checks to see if this weak pointer actually has a valid reference to an object
	 *
	 * @return  True if the weak pointer is valid and a pin operator would have succeeded
	 */
	 
	// 포인터가 가리키는 객체가 유효한지 확인
	[[nodiscard]] UE_FORCEINLINE_HINT bool IsValid() const
	{
		return Object != nullptr && WeakReferenceCount.IsValid();
	}
	
	...
	
}
```

```cpp file:SharedPointerInternals.h hlt:9,10
class FWeakReferencer
{
	...
		/**
		 * Tests to see whether or not this weak counter contains a valid reference
		 *
		 * @return  True if reference is valid
		 */
		// 제어 블록이 가리키는 대상이 유효한지 확인하는 함수
		// 해당 객체에 바로 접근하는 것이 아니라  GetSharedReferenceCount의 값으로 확인
		UE_FORCEINLINE_HINT const bool IsValid() const
		{
			return ReferenceController != nullptr && ReferenceController->GetSharedReferenceCount() > 0;
		}

	...
}
```