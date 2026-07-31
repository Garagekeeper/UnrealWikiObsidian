# [Niagara System](https://dev.epicgames.com/documentation/unreal-engine/overview-of-niagara-effects-for-unreal-engine?lang=ko)
나이아가라는 언리얼에서 제공하는 VFX 시스템이다. 코드 없이 많은 기능들을 추가할 수 있는 장점이 있다. 기본적으로 쉐이더를 BP로 생성 및 수정을 하는 것 과 비슷 하다.
![[2026.07.30 Unreal C++ 수업-1785401213970.webp|638x534]]
## 시스템
 이펙트 빌드에 필요한 모든 요소가 담긴 컨테이너
- Determinisim : 항상 같은 모양으로 나옴
- WarmuoTime : x초 후의 시간 상태로 시작
![[2026.07.30 Unreal C++ 수업-1785401664407.webp|466x551]]

## 이미터
- 파티클을 생성하고 제어하는 노드.
- 이미터 여러개를 조합해서 사용할 수 있다.
- 이미터는 스택으로 구성되고 내부에는 여러 그룹이 있다.
- 루프는 여기서 조정하는게 좋다.
- 프로퍼티
	- 기본 설정임
	- **Local Space** : 나이아가라가 움직일 때 같이 따라옴
	- **Determinisim** : 항상 같은 모양으로 나옴
	- **SimTarget** : 
		- CPU : 입자의 충돌 같은걸 구현할 수 있음
		- GPU : 뚫고 지나가도 상관없다. 같은 성능으로 입자를 1000배 더 많이 쓸 수 있다. 단 Dynamic 계산이 안됨
- 이미터 스폰
	- 이미터가 CPU에서 처음 생성될 때 일어나는 일을 정의 하는 그룹
- 이미터 업데이트
	- **Emitter state** : Lifecycle
	- **Spawn Per Unit** : 움직일 때 1센치당 몇개 생성할 지
	- **Spawn Rate**
- ParticleSpawn
	- **initialize Particle** : Life Cycle, Color, Position, Mass, Sprite, Mesh, Ribbon
	- **Shape Location** : 파티클이 나타나는 범위, cone으로 하면 가사의 깔때기 내부에서 소환, Distribution을 통해서 표면에서 할지 전체로 할지 설정 가능
	- **Add Velocity** : Linear, Point(사망으로 퍼져 나감), Cone
- **Particle Update**
	- **Gravity**
	- **Drag** : 공기 마찰 (멀리 못가게 하는 힘)
	- **Scale Color** : 시간에 따른 색상 변화
- **Render**
	- 여기서 머터리얼을 설정함
	- 플립북 구현 (애니메이션 처럼 보이는 파티클)

![[Niagara System-1785402702313.webp|307x858]]
## 모듈

## 파라미터

