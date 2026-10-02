# [ISM(Instanced Static Mesh Component)](https://dev.epicgames.com/documentation/unreal-engine/instanced-static-mesh-component-in-unreal-engine)
UE에서 StaticMesh의 최적화를 위해서 제공하는 컴포넌트로 반복되는 Static mesh의 최적화를 담당해준다. 조금 더 정확히 말하면 동일한 스태틱 메시의 Draw Call을 하나로 합쳐준다(원문에 따르면 하나의 액터에서 중복되는 스태틱 메시를 최적화 해준다.) 
사용하는 방법은 액터에 ISM을 추가하고 반복될 메시를 등록한 다음,  `AddInstance`를 통해서 원하는 회전과 위치에 원하는 만큼 배치할 수 있다. 또한 LOD, Culling과 관련한 설정도 가능하다.

ISM은 Culling과 관련한 단점이 있는데, 바로 내부 Instance중에서 하나라도 보이면(뷰 영역에 들어가면) 내부의 모든 인스턴스를 그려야하는 문제! (Dracall은 하나지만 위치와 같은 정보를 넘겨주니까는 비효율적)
또한 LOD도 개별로는 적용되지 않는다.

주로 좁은 구역에 반복되는 메시가 많을때 사용한다.