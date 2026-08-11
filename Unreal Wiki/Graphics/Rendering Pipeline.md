# [Rendering Pipeline](https://en.wikipedia.org/wiki/Graphics_pipeline)
Rendering Pipeline은 3차원 좌표계의 오브젝트를 2차원 좌표계로 변환하여 모니터에 그려주는 일련의 과정이다. 우리가 보는 화면을 Render즉 그려주는 과정으로 3차원 폴리곤 랜더링에서 자주 쓰이며 개념적으로 크게 4가지의 단계를 거친다

# Render Pipeline 개념
## Application
소프트웨어 혹은 CPU같은 메인 프로세서에서 실행되는 단계 충돌처리, 애니메이션등 여러 처리를 거쳐서 화면에 그릴 물체를 정하고 다음 단계로 넘겨준다.
여기서 Culling등을 통해 DrawCall자체를 줄이는 노력을 한다.

## Geometry
폴리곤과 관련한 연산들을 하는 단계이다. 세부적으로 5개의 과정을 거쳐서 월드좌표를 카메라 좌표계로 변경한다. 이 과정에서도 그리지 않을 부분은 잘라낸다.
- Model and camera transformations
	- 기본적으로 3차원에서 World좌표계는 가상으로 생성된 3차원 World의 좌표계를 의미한다.
	- Scene에 포함된 오브젝트들은 World좌표계를 기준으로 배치되어있다.
	- Scene이 있다면 Scene을 보여줄 Camera또한 필요하다. Camera는 Scene의 어느 지점을 보여줄지 정한다.
	- 여기서 월드 좌표계를 카메라 좌표계로 전환하는데, 가상의 카메라를 모니터 중앙에 박아놓는 과정이라고 생각하면 이해하기 쉽다.
		![[Rendering Pipeline-1786345740483.webp|806x387]]
- Projection
	- 카메라의 View 영역을 정육면체로 바뀌는 과정
	- 2가지 방식의 투영 존재
		- 원근 투영
			- 원근감 형성
		- 직교 투영
			- 원근감 없음 모두 같은 크기
	- 원근 투영의 원근감이 형성되는 과정은 다음과 같다
		- 카메라의 view는 절두체
		- 위의 그림중 카메라 좌표계 변환이 완료된 상태(오른쪽)에서 빨간점을 기준으로 View 영역을 정육면체로 투영한다고 생각해보자. 단순히 View의 좌표만 변경하는 것이 아니라 비율 자체가 늘어난다고 생각하면 편하다. 카메라와 가까운 평면은 비율 전체가 늘어나고, 카메라에서 먼 평면은 비율 전체가 줄어들어서 가까운 물체는 크게, 먼 물체는 작게 그릴 수 있게 된다.
		- 삼각형의 닮음비를 이용해 비율을 정한다.
		- 이래도 설명이 어렵다면, 풍선에 그림을 그려놓고 바람을 넣었다 빼면서 그림이 변화하는걸 생각해보자.
		![[Rendering Pipeline-1786346820350.webp|552]]
		![[Rendering Pipeline-1786346836948.webp]]
- Lighting
	- 여기서는 각 정점마다 빛을 계산하고 그 사이에 픽셀들은 각 점들의 보간으로 표현 ([[Gouraud Shading]]기반 설명)
		- [[Phong Shading]]도 많이 사용됨
- Clipping
	- 정육면체로 변환된 View의 바깥 부분에 있는 정점들은 그리지 않는다.
	 ![[Rendering Pipeline-1786346353467.webp]]
- Viewport transformations
	- 여기서 우리의 Display(Viewport)에 맞춘 변환이 진행된다
	- 화면 해상도에 맞춘 변환이 일어난다고 보면 된다.
	- 결과로 어떤 삼각형이 어떤 좌표에 그려질지 확정된다.

현대 하드웨어에서는 대부분의 기하적 계산이 vertex shader 단계에서 이루어진다. 최근에는 custom vertex shader 단계를 반드시 거쳐야하기도 한다고.

## Rasterization
변환된 좌표를 기반으로 해당 오브젝트를 어떤 픽셀들에 그려야 하는지 정해준다. 각 폴리곤을 
![[Rendering Pipeline-1786348435356.webp]]

![[Rendering Pipeline-1786349326904.webp]]

위 그림처럼 삼각형을 만들고, 그 삼각형을 프래그먼트(픽셀)들로 표현하는 것이 Rasterization 단계이다. 여기서 프래그먼트를 그리기 위해서 선을 그려야하고 선을 그리기 위해서 DDA등의 알고리즘을 사용한다. 


# 예시와 함께 살펴보는 Render Pipeline
매우 친절하게 설명되어있는 Vulkan의 Graphics Pipeline을 살펴보자. 사실 대부분의 그래픽 어플리케이션의 파이프라인은 큰 틀에서 비슷한 단계를 가진다.
![[Rendering Pipeline-1786349852366.webp]]
## InputAssembler
Application 단계에서 생성된 **정점 데이터를 가져와**서 Vertex Shader 단계에서 사용할 형식으로 가공한다. 이렇게 모은 **정점을 바탕으로 기본 도형을 그려준다**. `D3D_PRIMITIVE_TOPOLOGY`를 사용해서 기본 도형을 지정해 줄 수 있다. 여기서 추가로 **Index buffer** 라는 개념이 등장한다. 일반적으로 삼각형 2개를 그리려면 점이 6개가 필요하다. 하지만 2개의 삼각형이 정점을 공유한다면? 아래의 그림처럼 4개의 정점만으로 삼각형 2개를 그릴 수 있다. 
![[Rendering Pipeline-1786351221321.webp]]
삼각형을 그릴때 사용되는 정점들을 저장할 때 중복을 줄이기 위해서 **정점마다 index를 부여하고 중복된 index는 저장하지 않는**식으로 메모리와, 불필요한 계산을 줄일 수 있다. 도형을 그릴 때 필요한 정점들을 저장하는 것이기 때문에 이를 통해서 다양한 도형을 그릴 수 있다.

## Vertex Shader
Vertex Shader는 이전 단계에서 생성된 도형에 변환을 시키는 단계다. 모든 정점에 대해서 변환을 시행하며 결과적으로 model space를 Clip space로 변환해준다. 앞서 알아본 Geometry단계라고 생각하면 될 것 같다. (Clip space로 변환하면 자동으로 NDC로 간다는데 잘 몰루)

## Tessellation (선택적)
정점을 더 촘촘하게 구성해서 디테일한 표현을 할 때 사용. 원래 뜻은 같은 문양의 도형을 빈틈없이 채우는 것. LOD에 사용되는데, 가까운 정점은 더 촘촘하게 그리고 멀리 있는 정점들은 대충? 그리는 형태로 최적화가 진행된다.
![[Rendering Pipeline-1786352085495.webp]]![[Rendering Pipeline-1786352598901.webp]]
## The Geometry shader (선택적)
기본 도형에 정점을 추가하거나 삭제할 수 있는 셰이더로 Tessellation과 유사하지만 조금 더 유연하다. 하지만 대부분의 그래픽 카드에서는 성능이 좋지 못해서 사용되지 않는다.

## Rasteriztion
기본 도형을 fragments로 이산화 시키(잘게 쪼개)는 단계. (아날로그를 음악을 디지털로 바꾸는 과정을 생각하면 쉽다.) 프래그먼트들은 화면에 표시될 픽셀들이며 화면 밖으로 나가거나 다른 프래그먼트 뒤에 있는 프래그먼트들을 표시되지 않는다(Clipping과 Z-buffer/Depth-Buffer) 

## Fragment Shader (Pixel shader)
Rasteriztion에서 만들어진 Fragment들이 어떤 위치와 색상으로 그려질지 기록하는 단계로 텍스쳐의 좌표나 노말 벡터가 포함될 수 있다.

## Color blending
그래픽스 API에 따른 연산들 Ex) Alpha blending,  MSAA Resolve등의 추가 연산 실행

# Deferred Rendering
방금까지 살펴보던 방식은 전통적인 Forward Rendering이다. Forward Rendering에서는 광원마다 모든 정점을 수정하기 때문에 드로우콜이 광원이 많아지면 기하급수적으로 늘어난다. 이런 문제점을 해결하기 위해서 Deferred Rendering이 탄생했다. 많은 양의 라이팅을 실시간으로 처리하기 좋은 방식으로 정점에서 라이팅 계산이 필요한 요소 (Normal, Albedo, Depth, Diffuse)등을 모아놓고 프래그먼트 쉐이더라 끝난 뒤 한 번에 처리하는 방식으로 언리얼에서 이 방식을 기본으로 사용한다.
![[Rendering Pipeline-1786356985593.webp|759]]![[Rendering Pipeline-1786356991480.webp]]
하지만 장점이 있으면 단점이 있기 마련, 높은 대역폭을 요구하고 반투명 물체를 처리하기 어렵다. 픽셀 하나에 하나의 표면 정보만 기록하므로 알파 블렌딩을 자연스레 처리하기 어렵다. 간단한 해결책으로는 불투명 객체를 디퍼드로 그리고, 반투명 객체를 디퍼드로 그린다면 괜찮을지도.