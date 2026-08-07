# Linear Allocator
`A more efficient LinearAllocator than [UE::FLinearBlockAllocator](API\Runtime\Core\FLinearBlockAllocator), but one tuned for global use for Unreal's persistent core and engine allocations.` UE에서 만든 효율적인 선형 생성자로 오랫동안 유지되는 메모리 할당을 위해 최적화 되었다고 한다.

# 개념
이름 그대로 메모리를 선형으로 할당한다. 시작 주소와 할당이 끝난 지점의 주소만 알고 있으면 된다. 또한 메모리를 할당하려면 그냥 이전의 CurrentoffSet을 할당된 메모리의 시작 주소로 하고, 할당 사이즈만큼 오프셋을 뒤로 보내면 할당이 종료된다. 단순히 포인터 연산만 하기 때문에 `O(1)`의 시간 복잡도를 가진다. 하지만 개별적인 해제가 불가능하고 전체를 한번에 비워야하는 단점이 있다.
![[Linear Allocator-1786090736310.webp]]
![[Linear Allocator-1786090754635.webp|702x175]]