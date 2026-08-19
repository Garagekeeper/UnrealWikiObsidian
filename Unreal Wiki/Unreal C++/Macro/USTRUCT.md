# USTRUCT
UE에서 구조체를 리플렉션 시스템에 등록시키기 위한 매크로. [[UHT(Unreal Header Tool)]]에 의해서 파싱되어 코드가 생성된다. 이코드들을 기반으로 리플렉션시스템에 구조체를 등록한다. 이때 리플렉션 시스템에 등록되는 것은 구조체 자체가 아니라 해당 구조체를 표현하는 [[UScripStruct]]이다. [[UScripStruct]]는 구조체의 크기, 프로퍼티, 메타데이터 등의 정보를 가지고 cpp 구조체 타입을 리플렉션 시스템에서 표현한다.

따라서 구조체와 이를 표현하는 [[UScripStruct]]는 연관되어 있지만 서로 다른 객체다. 구조체는 실제 데이터를 저장하는 cpp 구조체이고, [[UScripStruct]]는 그 구조체를 표현하는 객체일뿐 데이터에 접근할 수 없다. USTRUCT()는 구조체를 UObject로 변환하거나 UObject를 상속시키는 매크로가 아니다. 여전히 일반 구조체이기 때문에 UObject를 대상으로하는 [[UnrealGC]], 오브젝트 포인터 타입을 사용할 수 없다.


```cpp file:InventoryComp.generated.h
struct Z_Construct_UScriptStruct_FInventorySlot_Statics;
#define FID_Ju_UEProjects_UnrealCpp_Source_Unreal_Cpp_Public_Component_InventoryComponent_h_18_GENERATED_BODY \
	friend struct ::Z_Construct_UScriptStruct_FInventorySlot_Statics; \
	UNREAL_CPP_API static class UScriptStruct* StaticStruct();


struct FInventorySlot;
```

```cpp file:InventoryComp.gen.cpp hlt:22-24,3-4,43-54
struct Z_Construct_UScriptStruct_FInventorySlot_Statics
{
	static inline consteval int32 GetStructSize() { return sizeof(FInventorySlot); }
	static inline consteval int16 GetStructAlignment() { return alignof(FInventorySlot); }
#if WITH_METADATA
	static constexpr UECodeGen_Private::FMetaDataPairParam Struct_MetaDataParams[] = {
		{ "BlueprintType", "true" },
		{ "ModuleRelativePath", "Public/Component/InventoryComponent.h" },
	};
	static constexpr UECodeGen_Private::FMetaDataPairParam NewProp_ItemData_MetaData[] = {
		{ "Category", "Slot" },
		{ "ModuleRelativePath", "Public/Component/InventoryComponent.h" },
		{ "NativeConstTemplateArg", "" },
	};
	static constexpr UECodeGen_Private::FMetaDataPairParam NewProp_StackCnt_MetaData[] = {
		{ "Category", "Slot" },
		{ "ModuleRelativePath", "Public/Component/InventoryComponent.h" },
	};
#endif // WITH_METADATA

// ********** Begin ScriptStruct FInventorySlot constinit property declarations ********************
	static const UECodeGen_Private::FObjectPropertyParams NewProp_ItemData;
	static const UECodeGen_Private::FIntPropertyParams NewProp_StackCnt;
	static const UECodeGen_Private::FPropertyParamsBase* const PropPointers[];
// ********** End ScriptStruct FInventorySlot constinit property declarations **********************
	static void* NewStructOps()
	{
		return (UScriptStruct::ICppStructOps*)new UScriptStruct::TCppStructOps<FInventorySlot>();
	}
	static const UECodeGen_Private::FStructParams StructParams;
}; // struct Z_Construct_UScriptStruct_FInventorySlot_Statics
static FStructRegistrationInfo Z_Registration_Info_UScriptStruct_FInventorySlot;
class UScriptStruct* FInventorySlot::StaticStruct()
{
	if (!Z_Registration_Info_UScriptStruct_FInventorySlot.OuterSingleton)
	{
		Z_Registration_Info_UScriptStruct_FInventorySlot.OuterSingleton = GetStaticStruct(Z_Construct_UScriptStruct_FInventorySlot, (UObject*)Z_Construct_UPackage__Script_Unreal_Cpp(), TEXT("InventorySlot"));
	}
	return Z_Registration_Info_UScriptStruct_FInventorySlot.OuterSingleton;
	}

// ********** Begin ScriptStruct FInventorySlot Property Definitions *******************************
const UECodeGen_Private::FObjectPropertyParams Z_Construct_UScriptStruct_FInventorySlot_Statics::NewProp_ItemData = { "ItemData", 
nullptr, 
(EPropertyFlags)0x0114000000000015, 
UECodeGen_Private::EPropertyGenFlags::Object | UECodeGen_Private::EPropertyGenFlags::ObjectPtr, RF_Public|RF_Transient|RF_MarkAsNative, 
nullptr, 
nullptr, 
1, 
STRUCT_OFFSET(FInventorySlot, ItemData), 
Z_Construct_UClass_UItemDataAsset_NoRegister,
METADATA_PARAMS(UE_ARRAY_COUNT(NewProp_ItemData_MetaData), 
NewProp_ItemData_MetaData) };

const UECodeGen_Private::FIntPropertyParams Z_Construct_UScriptStruct_FInventorySlot_Statics::NewProp_StackCnt = { 
"StackCnt", 
nullptr, 
(EPropertyFlags)0x0020080000000015, 
UECodeGen_Private::EPropertyGenFlags::Int, 
RF_Public|RF_Transient|RF_MarkAsNative, 
nullptr, 
nullptr, 
1, 
STRUCT_OFFSET(FInventorySlot, StackCnt), 
METADATA_PARAMS(UE_ARRAY_COUNT(NewProp_StackCnt_MetaData), 
NewProp_StackCnt_MetaData) };

const UECodeGen_Private::FPropertyParamsBase* const Z_Construct_UScriptStruct_FInventorySlot_Statics::PropPointers[] = {
	(const UECodeGen_Private::FPropertyParamsBase*)&Z_Construct_UScriptStruct_FInventorySlot_Statics::NewProp_ItemData,
	(const UECodeGen_Private::FPropertyParamsBase*)&Z_Construct_UScriptStruct_FInventorySlot_Statics::NewProp_StackCnt,
};
static_assert(UE_ARRAY_COUNT(Z_Construct_UScriptStruct_FInventorySlot_Statics::PropPointers) < 2048);
// ********** End ScriptStruct FInventorySlot Property Definitions *********************************
const UECodeGen_Private::FStructParams Z_Construct_UScriptStruct_FInventorySlot_Statics::StructParams = {
	(UObject* (*)())Z_Construct_UPackage__Script_Unreal_Cpp,
	nullptr,
	&NewStructOps,
	"InventorySlot",
	Z_Construct_UScriptStruct_FInventorySlot_Statics::PropPointers,
	UE_ARRAY_COUNT(Z_Construct_UScriptStruct_FInventorySlot_Statics::PropPointers),
	sizeof(FInventorySlot),
	alignof(FInventorySlot),
	RF_Public|RF_Transient|RF_MarkAsNative,
	EStructFlags(0x00000001),
	METADATA_PARAMS(UE_ARRAY_COUNT(Z_Construct_UScriptStruct_FInventorySlot_Statics::Struct_MetaDataParams), Z_Construct_UScriptStruct_FInventorySlot_Statics::Struct_MetaDataParams)
};
UScriptStruct* Z_Construct_UScriptStruct_FInventorySlot()
{
	if (!Z_Registration_Info_UScriptStruct_FInventorySlot.InnerSingleton)
	{
		UECodeGen_Private::ConstructUScriptStruct(Z_Registration_Info_UScriptStruct_FInventorySlot.InnerSingleton, Z_Construct_UScriptStruct_FInventorySlot_Statics::StructParams);
	}
	return CastChecked<UScriptStruct>(Z_Registration_Info_UScriptStruct_FInventorySlot.InnerSingleton);
}
// ********** End ScriptStruct FInventorySlot ******************************************************

```