# Unreal Study Hub

> [!abstract] 언리얼 C++ 학습의 출발점
> 개념은 주제별로, 수업은 날짜별로 찾아보세요. 아래 색인은 내용이 작성된 노트로 연결됩니다.

## 빠른 이동

**[[StudyLog/학습 기록|학습 기록 전체 보기]]** · [[#주제별 개념 노트|개념 노트]] · [[#학습 흐름|학습 흐름]]

### 최근 작성된 수업

- [[2026.09.30 Unreal C++ 수업|2026.09.30 · PlayerState]]
- [[2026.09.29 Unreal C++ 수업|2026.09.29 · RPC]]
- [[2026.09.22 Unreal C++ 수업|2026.09.22 · GameplayCue와 Effect 적용]]

## 주제별 개념 노트

| 카테고리 | 다루는 내용 | 노트 |
| --- | --- | ---: |
| [[#C++ 기초와 디자인 패턴\|C++ 기초와 디자인 패턴]] | 값의 성질, 복사·이동과 반환값 최적화, 객체 설계 패턴. | 5 |
| [[#언리얼 C++와 리플렉션\|언리얼 C++와 리플렉션]] | UObject 타입 정보와 코드 생성, 매크로 및 오류 검사. | 21 |
| [[#메모리 관리\|메모리 관리]] | 스마트 포인터, UObject 가비지 컬렉션과 메모리 할당 방식. | 9 |
| [[#게임플레이\|게임플레이]] | 액터 생성, 데미지 적용과 난수 생성. | 3 |
| [[#물리와 공간 쿼리\|물리와 공간 쿼리]] | 선과 도형을 투사하여 공간과 충돌을 검사하는 방법. | 2 |
| [[#네트워크\|네트워크]] | 연결, 액터 역할, 복제, 원격 호출과 관련성 판정. | 5 |
| [[#그래픽과 최적화\|그래픽과 최적화]] | 렌더링 파이프라인과 반복 메시의 인스턴싱. | 3 |
| [[#UI와 Slate\|UI와 Slate]] | 위젯 구조, 좌표 정보와 입력 이벤트 처리. | 3 |
| [[#애니메이션과 VFX\|애니메이션과 VFX]] | 몽타주 재생과 Niagara 이펙트 구성. | 2 |

## C++ 기초와 디자인 패턴

값의 성질, 복사·이동과 반환값 최적화, 객체 설계 패턴.

### 값과 이동

- [[Unreal Wiki/C++/Value Semantics/NRVO|NRVO]]
- [[Unreal Wiki/C++/Value Semantics/rvalue, lvalue|rvalue, lvalue]]
- [[Unreal Wiki/C++/Value Semantics/RVO|RVO]]
- [[Unreal Wiki/C++/Value Semantics/Value Category|Value Category]]

### 디자인 패턴

- [[Unreal Wiki/C++/Design Patterns/Strategy 패턴|Strategy 패턴]]

## 언리얼 C++와 리플렉션

UObject 타입 정보와 코드 생성, 매크로 및 오류 검사.

### 리플렉션과 지정자

- [[Unreal Wiki/Unreal C++/Reflection/DefaultToInstanced|DefaultToInstanced]]
- [[Unreal Wiki/Unreal C++/Reflection/EditInlineNew|EditInlineNew]]
- [[Unreal Wiki/Unreal C++/Reflection/GetClass()|GetClass()]]
- [[Unreal Wiki/Unreal C++/Reflection/StaticClass()|StaticClass()]]
- [[Unreal Wiki/Unreal C++/Reflection/UFUNCTION|UFUNCTION]]
- [[Unreal Wiki/Unreal C++/Reflection/UHT(Unreal Header Tool)|UHT(Unreal Header Tool)]]
- [[Unreal Wiki/Unreal C++/Reflection/UPROPERTY|UPROPERTY]]
- [[Unreal Wiki/Unreal C++/Reflection/USTRUCT|USTRUCT]]

### 매크로와 코드 생성

- [[Unreal Wiki/Unreal C++/Macro/__LINE__|__LINE__]]
- [[Unreal Wiki/Unreal C++/Macro/BODY_MACRO_COMBINE(A,B,C,D)|BODY_MACRO_COMBINE(A,B,C,D)]]
- [[Unreal Wiki/Unreal C++/Macro/CURRENT_FILE_ID|CURRENT_FILE_ID]]
- [[Unreal Wiki/Unreal C++/Macro/DECLARE_CLASS2|DECLARE_CLASS2]]
- [[Unreal Wiki/Unreal C++/Macro/DECLARE_FUNCTION|DECLARE_FUNCTION]]
- [[Unreal Wiki/Unreal C++/Macro/ENUM_CLASS_FLAGS()|ENUM_CLASS_FLAGS()]]
- [[Unreal Wiki/Unreal C++/Macro/Macro 모음|Macro 모음]]
- [[Unreal Wiki/Unreal C++/Macro/PRAGMA_DISABLE_DEPRECATION_WARNINGS|PRAGMA_DISABLE_DEPRECATION_WARNINGS]]
- [[Unreal Wiki/Unreal C++/Macro/PRAGMA_ENABLE_DEPRECATION_WARNINGS|PRAGMA_ENABLE_DEPRECATION_WARNINGS]]
- [[Unreal Wiki/Unreal C++/Macro/ProjectName_API|ProjectName_API]]

### Assert

- [[Unreal Wiki/Unreal C++/Macro/Assert/check(expreesion)|check(expreesion)]]
- [[Unreal Wiki/Unreal C++/Macro/Assert/ensure(expression)|ensure(expression)]]
- [[Unreal Wiki/Unreal C++/Macro/Assert/verify(expression)|verify(expression)]]

## 메모리 관리

스마트 포인터, UObject 가비지 컬렉션과 메모리 할당 방식.

### 스마트 포인터

- [[Unreal Wiki/Memory/Smart Pointers/2026-06-22-SmartPointer(Blog)|2026-06-22-SmartPointer(Blog)]]
- [[Unreal Wiki/Memory/Smart Pointers/Smart Pointer in UE|Smart Pointer in UE]]
- [[Unreal Wiki/Memory/Smart Pointers/TSharedPtr|TSharedPtr]]
- [[Unreal Wiki/Memory/Smart Pointers/TSharedRef|TSharedRef]]
- [[Unreal Wiki/Memory/Smart Pointers/TUniquePtr|TUniquePtr]]
- [[Unreal Wiki/Memory/Smart Pointers/TWeakPtr|TWeakPtr]]

### 가비지 컬렉션

- [[Unreal Wiki/Memory/Garbage Collection/2026-07-24-unrealGc(Blog)|2026-07-24-unrealGc(Blog)]]

### 할당자

- [[Unreal Wiki/Memory/Allocators/Linear Allocator|Linear Allocator]]
- [[Unreal Wiki/Memory/Allocators/Persistent Linear Allocator|Persistent Linear Allocator]]

## 게임플레이

액터 생성, 데미지 적용과 난수 생성.

- [[Unreal Wiki/Gameplay/ApplyDamage 시리즈|ApplyDamage 시리즈]]
- [[Unreal Wiki/Gameplay/RandomStream|RandomStream]]
- [[Unreal Wiki/Gameplay/SpawnActor()|SpawnActor()]]

## 물리와 공간 쿼리

선과 도형을 투사하여 공간과 충돌을 검사하는 방법.

- [[Unreal Wiki/Physics/Line Trace|Line Trace]]
- [[Unreal Wiki/Physics/Sweep|Sweep]]

## 네트워크

연결, 액터 역할, 복제, 원격 호출과 관련성 판정.

- [[Unreal Wiki/Network/Actor Relevancy|Actor Relevancy]]
- [[Unreal Wiki/Network/Connection|Connection]]
- [[Unreal Wiki/Network/NetRole|NetRole]]
- [[Unreal Wiki/Network/Replication|Replication]]
- [[Unreal Wiki/Network/RPC|RPC]]

## 그래픽과 최적화

렌더링 파이프라인과 반복 메시의 인스턴싱.

### 렌더링

- [[Unreal Wiki/Graphics/Rendering Pipeline|Rendering Pipeline]]

### 인스턴싱 최적화

- [[Unreal Wiki/Graphics/Optimization/HISM|HISM]]
- [[Unreal Wiki/Graphics/Optimization/ISM|ISM]]

## UI와 Slate

위젯 구조, 좌표 정보와 입력 이벤트 처리.

- [[Unreal Wiki/UI/FGeometry|FGeometry]]
- [[Unreal Wiki/UI/FReply|FReply]]
- [[Unreal Wiki/UI/SWidget|SWidget]]

## 애니메이션과 VFX

몽타주 재생과 Niagara 이펙트 구성.

### 애니메이션

- [[Unreal Wiki/Animation/몽타쥬(Montage)|몽타쥬(Montage)]]

### VFX

- [[Unreal Wiki/VFX/Niagara System|Niagara System]]

## 학습 흐름

| 공부할 주제 | 추천 순서 |
| --- | --- |
| 언리얼 객체와 리플렉션 | [[UHT(Unreal Header Tool)\|UHT]] → [[UPROPERTY]] · [[UFUNCTION]] · [[USTRUCT]] → [[StaticClass()]] · [[GetClass()]] |
| 메모리와 소유권 | [[rvalue, lvalue]] → [[Smart Pointer in UE]] → [[TSharedPtr]] · [[TWeakPtr]] · [[TUniquePtr]] → [[2026-07-24-unrealGc(Blog)\|Unreal GC]] |
| 네트워크 | [[Connection]] → [[NetRole]] → [[Replication]] → [[RPC]] → [[Actor Relevancy]] |
| 인벤토리와 UI | [[2026.08.13 Unreal C++ 수업\|인벤토리]] → [[SWidget]] · [[FGeometry]] · [[FReply]] → [[Strategy 패턴]] |
| GAS | [[2026.09.15 Unreal C++ 수업\|기초·Attribute]] → [[2026.09.16 Unreal C++ 수업\|Effect]] → [[2026.09.18 Unreal C++ 수업\|Ability]] → [[2026.09.21 Unreal C++ 수업\|Task]] → [[2026.09.22 Unreal C++ 수업\|Cue]] |

---

> [!tip] 정리 기준
> 개념 노트는 기능과 주제로 분류했습니다. 여러 주제가 섞인 강의 기록은 `StudyLog`에 모았으며, 이미지와 그림은 기존 보관 위치를 유지합니다. 빈 노트와 작성 전 항목은 기존 위치에 남겨 두었습니다.
