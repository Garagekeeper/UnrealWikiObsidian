# TUniquePtr 
`std::unique_ptr`을 UE에서 구현한 버전이다. 주의할 점은 `UObject System`에 의해서 관리되는 객체에는 사용할 수 없다.

`UniquePtr`은 다른 스마트 포인터들과는 달리 **유일한 소유권**을 부여할 때 사용된다. UE의 설명에 따르면 객체의 생명주기가 하나의 포인터에 의해서만 관리되기를 바랄때 사용한다. 소유한다는 개념이기 때문에 복사는 금지된다. 하지만 [[rvalue, lvalue]]에서 살펴보았던 `이동`은 가능하다. 이동은 소유권 자체가 옮겨가는 개념이라고 생각하면 이해가 쉽다. UE에서는 `MoveTemp()`를 통해서 이동이 가능하다.

```cpp file:UniquePtr.h hlt:12,7-9

// Single-ownership smart pointer in the vein of std::unique_ptr.
// Use this when you need an object's lifetime to be strictly bound to the lifetime of a single smart pointer.
//
// This class is non-copyable - ownership can only be transferred via a move operation, e.g.:
//
// TUniquePtr<MyClass> Ptr1(new MyClass);    // The MyClass obj is owned by Ptr1.
// TUniquePtr<MyClass> Ptr2(Ptr1);           // Error - TUniquePtr is not copyable
// TUniquePtr<MyClass> Ptr3(MoveTemp(Ptr1)); // Ptr3 now owns the MyClass object - Ptr1 is now nullptr.

	// Non-copyable
	// 복사를 방지하기 위해서 복사 생성자와 복사 대입 연산자를 삭제
	TUniquePtr(const TUniquePtr&) = delete;
	TUniquePtr& operator=(const TUniquePtr&) = delete;
```