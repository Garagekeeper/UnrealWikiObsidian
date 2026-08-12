# NRVO(Named Return Value Optimization)
컴파일러가 수행하는 반환값 최적화의 한 종류로 함수가 **지역객체**(주로 이름이 있는 객체)를 반환할때 복사/이동이 생략되고 함수를 호출한 객체의 스택 메모리에 반환값을 바로 생성하는 것을 의미한다.
원래는 반환값을 호출한 객체에 복사하는 과정이 있어야했는데 이를 줄여준다. 조건을 충족한다고 무조건 적용되는 것은 아니고 적용이 안된경우 복사가 아니라 이동을 우선으로 적용해준다.

```cpp title:NRVO
TUniquePtr<FMazeData> AMazeActor::MakeMazeData()
{
	TUniquePtr<FMazeData> Maze = MakeUnique<FMazeData>();
	Maze->MakeMaze(Width, Height, Seed, AlgoType);

	// Maze 자체는 Lvalue이지만, NRVO를 시도해서 호출자 위치에 바로 생성하거나 실패시 이동시킴
	// 여기서 std::move 혹은 MoveTemp()를 사용하면 NRVO의 이점을 활용하지 못함.
	return Maze;
}
```