# UPROPERTY
UPROPERTY는 변수를 UObject시스템에 등록하기 위한(Reflection System) 매크로다. 프로젝트가 컴파일 될 때 [[UHT(Unreal Header Tool)]]가 이 매크로를 파싱해서 이 속성에 필요한 코드들을 생성해준다. 이 과정에서 UPROPERTY 자체는 공백으로 비워지지만 `.gen.cpp` 파일에서 구조체로 변수들을 저장하고 `.generated.h`에서 실제 클래스를 생성할 때 구조체를 넘긴다.

```cpp file:ATestActor.gen.cpp
// int 변수의 UPROPERTY 예시
const UECodeGen_Private::FIntPropertyParams Z_Construct_UClass_ATestActor_Statics::NewProp_Data2_1 = { "Data2_1", // 변수 이름
nullptr, //Display Name
(EPropertyFlags)0x0020080000020001, // Keyword Flag
UECodeGen_Private::EPropertyGenFlags::Int, // 자료형
RF_Public|RF_Transient|RF_MarkAsNative, // 오브젝트 플래그, (외부 접근, 일회성, C++ native 표시)
nullptr, // 잘 모름
nullptr, // 잘 모름 둘 다 확장 부가 기능 포인터 (RepNotify 함수 이름) 같은거 라고 하긴함
1, // 배열의 크기
STRUCT_OFFSET(ATestActor, Data2_1), //메모리 오프셋 ATestActor 기준으로
METADATA_PARAMS(UE_ARRAY_COUNT(NewProp_Data2_1_MetaData), NewProp_Data2_1_MetaData) }; // 메타데이터 파라미터
```

```cpp file:ATestActor.gen.cpp
// 실제 클래스 생성시 위에서 만든 //Z_Construct_UClass_ATestActor_Statics를 사용해서 생성

#define FID_Ju_UEProjects_UnrealCpp_Source_Unreal_Cpp_Public_TestActor_h_12_INCLASS_NO_PURE_DECLS \
private: \
	static void StaticRegisterNativesATestActor(); \
	friend struct ::Z_Construct_UClass_ATestActor_Statics; \
	static UClass* GetPrivateStaticClass(); \
	friend UNREAL_CPP_API UClass* ::Z_Construct_UClass_ATestActor_NoRegister(); \
public: \
	DECLARE_CLASS2(ATestActor, AActor, COMPILED_IN_FLAGS(0 | CLASS_Config), CASTCLASS_None, TEXT("/Script/Unreal_Cpp"), Z_Construct_UClass_ATestActor_NoRegister) \
	DECLARE_SERIALIZER(ATestActor)

```

* KeyWord
	* **VisibleAnywhere**: 모든 속성창에 표시
	* **VisibleDefaultOnly**: 클래스 디폴드에서만 표시
	* **VisibleInstanceOnly**: 맵에 배치된 액터의 디테일 창에서만
	* **EditAnywhere**: 어디서나 수정
	* **EditDefaultsOnly**: 클래스 디폴트에서만 수정
	* **EditInstanceOnly**: 배치된 액터의 디테일에서만 수정
	* **BlueprintReadOnly**: BP에서 읽기만
	* **BlueprintReadWrite**: BP에서 읽기 쓰기 다 가능

![[Pasted image 20260720174628.png|197]]

![[Pasted image 20260720174641.png|635]]
(VisibleAnywhere, VisibleDefaultOnly, EditAnywhere, EditDefaultsOnly 가 적용된 모습, VisibleInstanceOnly 와 EditInstanceOnly가 적용된 변수는 보이지 않음)


![[Pasted image 20260720175003.png|501]]
(VisibleAnywhere, VisibleInstanceOnly, EditAnywhere, EditInstanceOnly 가 적용된 모습, VisibleDefaultOnly 와 EditDefaultsOnly 적용된 변수는 보이지 않음)


