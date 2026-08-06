# Smart Pointer in UE
C++11에 추가된 표준 스마트 포인터와 non-nullable shared pointer 역할의 Shared Reference가 구현되어 있다.


# SmartPointer Library
[[TSharedPtr]]
[[TWeakPtr]]
[[TUniquePtr]] 
[[TSharedRef]]

## 팁
TSharedPtr 생성은 `MakeShared`를 활용하자. new를 이용해 스마트 포인터를 생성하면 대상 포인터 따로, 제어블록을 따로 할당하기 떄문에 비효율 적이다. 반면 `MakeShared/MakeUnique` 사용시 한번에 할당을 해준다.  

UE SmartPointer Library간의 형 변환은 전용 헬퍼 함수를 사용하자.
- `StaticCastSharedPtr<T>()` / `StaticCastSharedRef<T>()`
	- DownCasting을 위해서 새용
- `ConstCastSharedPtr<T>()` / `ConstCastSharedRef<T>()`
	- const 붙은걸 뗄때 사용