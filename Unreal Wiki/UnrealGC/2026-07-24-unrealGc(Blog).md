---
title: Smart Pointer
date: 2026-07-24 09:02:46 +0900
description: ""
categories:
  - Unreal Engine
  - GC
tags:
  - cpp
  - unreal
  - memory_management
  - unreal_gc
math: true
mermaid: true
---
 
 <br>

# 언리얼 GC
 언리얼은 Cpp에서 제공하지 않는 GC(Garbage Collecting)기능을 제공한다. 기본적으로 Mark And Sweep 방식의 GC이며 리플렉션 시스템에 포함된 객체들만 관리한다. (UObject Systme). 이를 위해서 UPROPERTY, UCLASS등의 매크로를 통해서 변수, 클래스들을 리플렉션 시스템에 등록시킨다. 이렇게 런타임에 메모리가 UObject타입이면 GC가 가능해진다.
 
## GC 근거
언리얼 GC는 오직 **루트**에서 해당 객체까지 닿는지를 놓고 가비지 콜렉팅을 한다. 루트에서 닿지 않는 객체들은 추후에 수거된다. 순환 참조 형태더라도 루트에서 닿지 않으면 대상이 아니다. 루트에서 닿으려면 엄청난 든다고 생각할 수 있다. 하짐나 거대한 나무 뿌리에 직접 연결된 형태는 아니고 Root 밑에 Root Set들이 있고 그 밑에 메모리들이 달린다. (Root Set이 도달 가능하면 Root에서도 도달 가능, Root Set은 Root에 연결되어 있으니까)
 


