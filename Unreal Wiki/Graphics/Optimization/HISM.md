# [HISM(Hierarchical Instanced Static Mesh)](https://dev.epicgames.com/documentation/unreal-engine/instanced-static-mesh-component-in-unreal-engine)

ISM의 Culling 문제를 해결하기 위해서 도입. 인스턴스를 클러스터(공간 기준)로 나누어 계층 구조로 관리한다. 따라서 카메라 밖에 있는 클러스터는 Culling해 조금 더 효율적으로 사용할 수 있게 되었다. LOD또한 인스턴스 단위로 적용이 가능해졌다. 넓은 구역에 퍼진 메시 (숲, 바위 등)
![[HISM-1786526477084.webp|454]]

```cpp Title:How_to_Use_HISM
	UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Maze|Components")
	TObjectPtr<UHierarchicalInstancedStaticMeshComponent> FloorHISM = nullptr;
	
	//원하는 회전값과 위치에 매시를 배치
	FloorHISM->AddInstance(FTransform(FRotator::ZeroRotator, InLocation));
```

![[HISM-1786526637247.webp]]![[HISM-1786526657631.webp|170x178]]
사용 전후의 극명한 차이