# TSharedRef
TSharedRef은 UE에만 있는 스마트 포인터로 언제나 [[TSharedPtr]]로 변환될 수 있고 거의 동일한 기능을 하지만 **null이 불가능한 객체를 참조**해야한다. **참조한 오브젝트가 null이 아님을 보장**할때 사용한다.

```cpp file:SharedPointer.h hlt:8,34,23
class TSharedRef
{
	/**
	 * Constructs default shared reference that owns the default object for specified type.
	 *
	 * Used internally only. Please do not use!
	 */
	 // 기본 생성자를 사용하지 말라고 강조하고 있다.
	 // 기본생성자에 nullptr이 들어가느 것을 막기위해 new ObjectType()를 넣어줌
	TSharedRef()
		: Object(new ObjectType())
		, SharedReferenceCount(SharedPointerInternals::NewDefaultReferenceController<Mode>(Object))
	{
		EnsureRetrievingVTablePtrDuringCtor(TEXT("TSharedRef()"));
		Init(Object);
	}

	...
	// 포인터를 인자로 받는 생성자
	inline explicit TSharedRef( OtherType* InObject )
		: Object( InObject )
		, SharedReferenceCount( SharedPointerInternals::NewDefaultReferenceController< Mode >( InObject ) )
	{
		Init(InObject);
	}
	
	...
	
		template<class OtherType>
	void Init(OtherType* InObject)
	{
		// If the following assert goes off, it means a TSharedRef was initialized from a nullptr object pointer.
		// Shared references must never be nullptr, so either pass a valid object or consider using TSharedPtr instead.
		// nullptr이 들어오면 바로 크래시
		check(InObject != nullptr);

		// If the object happens to be derived from TSharedFromThis, the following method
		// will prime the object with a weak pointer to itself.
		SharedPointerInternals::EnableSharedFromThis(this, InObject);
	}
}
```