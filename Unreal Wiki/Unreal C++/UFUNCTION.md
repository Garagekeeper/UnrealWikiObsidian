# UFUNCTION
UFUNCTION은 함수를 Reflection System에 등록하기 위한 매크로다.
프고젝트가 컴파일 될 때 [[UHT(Unreal Header Tool)]]가 이 매크로를 파싱해서 이 함수에 필요한 코드들을 생성해준다. 이후에 UFUNCTION 자체는 비워지지만 `.generated.h`에 함수의 Specifier에 따라 `DECLARE_FUNCTION(execXXX)`, RPC 함수, 이벤트 함수 등의 코드가 생성된다.

```cpp file:ATestActor.generated.cpp

#define FID_Ju_UEProjects_UnrealCpp_Source_Unreal_Cpp_Public_TestActor_h_12_RPC_WRAPPERS_NO_PURE_DECLS \

//BlueprintNativeEvent에서 호출될 기본 함수
//BP에서 오버라이드 안하면 이게 호출된다.
virtual void Test_NativeEnventFunc_Implementation(); \
//BlueprintNativeEvent를 위한 BP용 execTest 생성
DECLARE_FUNCTION(execTest_NativeEnventFunc); \
// Reflection System이 호출하는 exec 함수 생성 
// Blueprint에서도 이 exec 함수를 통해 호출된다.
DECLARE_FUNCTION(execTest_UFunction);

//BlueprintImplementableEvent의 경우는 .gen.cpp에서 구현부를 알아서 작성
//ProcessEvent를 이용해서 연결해줌
```

## Keyword
* **BlueprintCallable**: cpp에서 작성한 코드를 BP에서 호출 가능
* **BlueprintImplementableEvent**: cpp에서 선언만, BP에서 구현
	* 커스텀 이벤트로 만들면 안되고 반드시 오버라이드에서 뽑아 써야함
	*  .gen.cpp에서 구현해주는 형태
* **BlueprintNativeEvent**: cpp에서 선언 및 구현 가능, BP에서 재정의 가능
		* cpp에서 함수 이름 뒤에 \_Implementation을 붙여서 구현 해야함.
		* 커스텀 이벤트로 만들면 안되고 반드시 오버라이드에서 뽑아 써야함
* **BlueprintPure**: 실행핀 없이 호출되는 함수(순서에 상관 없이)(BP에서 녹색으로 표시되는 노드들)
	* Get 계열 함수에 적합
		
```cpp file:BlueprintNativeEvent_Example
	UFUNCTION(BlueprintNativeEvent)
	void Attack();
		  
	void APlayer::Attack_Implementation() { }
```

<br>



## UHT가 UFUNCTION의 정보를 저장 하는 법

```cpp file:TestActor.gen.cpp
//BlueprintImplementableEvent 키워드 함수의 생성코드
//구현을 BP에서하는데, 그렇다고 없으면 링크 에러가 나니까 끼워넣은 것
//cpp에서 이걸 호출하면 리플렉션 시스템에서 해당 함수를 찾아서 실행해줌
static FName NAME_ATestActor_Test_ImplementableFunc = FName(TEXT("Test_ImplementableFunc"));

void ATestActor::Test_ImplementableFunc()
{
// 함수의 이름(FName)을 통해서 UFunction 포인터를 가져옴
    UFunction* Func = FindFunctionChecked(NAME_ATestActor_Test_ImplementableFunc);
    //ProcessEvent를 통해서 UFunction호출
    ProcessEvent(Func,NULL);
}

// 해당 UFUNCTION의 정보를 담는 구조체
struct Z_Construct_UFunction_ATestActor_Test_ImplementableFunc_Statics
{
#if WITH_METADATA
    static constexpr UECodeGen_Private::FMetaDataPairParam Function_MetaDataParams[] = {

        { "ModuleRelativePath", "Public/TestActor.h" },
    };

#endif // WITH_METADATA
	// 여기에 함수들이 파라미터로 저장됨
    static const UECodeGen_Private::FFunctionParams FuncParams;
};

```

<br>

```cpp hlt:2|FuncParams file:TestActor.gen.cpp
// 함수들을 구조체로 만들어서 등록
const UECodeGen_Private::FFunctionParams Z_Construct_UFunction_ATestActor_Test_ImplementableFunc_Statics::FuncParams = 
{ 
	{   (UObject*(*)())Z_Construct_UClass_ATestActor,
		nullptr,
		"Test_ImplementableFunc",
		nullptr,
		0,
		0,
		RF_Public|RF_Transient|RF_MarkAsNative,
		(EFunctionFlags)0x08080800,
		0,
		0, 
		METADATA_PARAMS(UE_ARRAY_COUNT(Z_Construct_UFunction_ATestActor_Test_ImplementableFunc_Statics::Function_MetaDataParams), 
		Z_Construct_UFunction_ATestActor_Test_ImplementableFunc_Statics::Function_MetaDataParams)
	},  
};

//여기서 실제 UFunction이 만들어진다.
//Uobjcet를 상속받은 클래스가 만들어지면서 호출되는듯
UFunction* Z_Construct_UFunction_ATestActor_Test_ImplementableFunc()
{
    static UFunction* ReturnFunction = nullptr;
    if (!ReturnFunction)
    {
        UECodeGen_Private::ConstructUFunction(&ReturnFunction, Z_Construct_UFunction_ATestActor_Test_ImplementableFunc_Statics::FuncParams);
    }
    return ReturnFunction;
}

//Z_Construct_UClass_ATestActor_Statics의 일부
//이렇게 함수들의 정보를 링크 인포로 저장, 나중에 Z_Construct_UClass_ATestActor가 호출되면 순차적으로 호출됨
static constexpr FClassFunctionLinkInfo FuncInfo[] = {
	{ &Z_Construct_UFunction_ATestActor_Test_ImplementableFunc, "Test_ImplementableFunc" }, // 3451430316
	{ &Z_Construct_UFunction_ATestActor_Test_NativeEnventFunc, "Test_NativeEnventFunc" }, // 3696412700
	{ &Z_Construct_UFunction_ATestActor_Test_UFunction, "Test_UFunction" }, // 964030761
};

//이 함수가 호출되면 UCLASS가 반환된다.
//내부에서 ConstructUClass를 통해 UCLASS를 생성한다.
UClass* Z_Construct_UClass_ATestActor()
{
	if (!Z_Registration_Info_UClass_ATestActor.OuterSingleton)
	{
		UECodeGen_Private::ConstructUClass(Z_Registration_Info_UClass_ATestActor.OuterSingleton, Z_Construct_UClass_ATestActor_Statics::ClassParams);
	}
	return Z_Registration_Info_UClass_ATestActor.OuterSingleton;
}

```

<br>


```cpp file:TestActor.gen.cpp
// ********** Begin Registration *******************************************************************
struct Z_CompiledInDeferFile_FID_Ju_UEProjects_UnrealCpp_Source_Unreal_Cpp_Public_TestActor_h__Script_Unreal_Cpp_Statics
{
	static constexpr FClassRegisterCompiledInInfo ClassInfo[] = {
		{ 
			Z_Construct_UClass_ATestActor, 
			ATestActor::StaticClass, TEXT("ATestActor"), 
			&Z_Registration_Info_UClass_ATestActor, 
			CONSTRUCT_RELOAD_VERSION_INFO(FClassReloadVersionInfo, 
			sizeof(ATestActor), 
			2310442574U) 
		},
	};
}; // Z_CompiledInDeferFile_FID_Ju_UEProjects_UnrealCpp_Source_Unreal_Cpp_Public_TestActor_h__Script_Unreal_Cpp_Statics 

// 이 전역 구조체가 생성되면서 Deffered Registry에 등록
static FRegisterCompiledInInfo Z_CompiledInDeferFile_FID_Ju_UEProjects_UnrealCpp_Source_Unreal_Cpp_Public_TestActor_h__Script_Unreal_Cpp_1416214363{
	TEXT("/Script/Unreal_Cpp"),
	Z_CompiledInDeferFile_FID_Ju_UEProjects_UnrealCpp_Source_Unreal_Cpp_Public_TestActor_h__Script_Unreal_Cpp_Statics::ClassInfo,
	UE_ARRAY_COUNT(Z_CompiledInDeferFile_FID_Ju_UEProjects_UnrealCpp_Source_Unreal_Cpp_Public_TestActor_h__Script_Unreal_Cpp_Statics::ClassInfo),
	nullptr, 
	0,
	nullptr, 
	0,
};
```


<br>

```cpp file:UObjectBase hl:10
#if !UE_WITH_CONSTINIT_UOBJECT 
/**
 * Helper class to perform registration of object information.  It blindly forwards a call to RegisterCompiledInInfo
 */
struct FRegisterCompiledInInfo
{
	template <typename... ArgTypes>
	FRegisterCompiledInInfo(ArgTypes&&... Args)
	{
		RegisterCompiledInInfo(Forward<ArgTypes>(Args)...);
	}
};
#endif // !UE_WITH_CONSTINIT_UOBJECT
```

<br>

```cpp File:UObjectGlobals.cpp hlt:4-27|RegisterCompiledInInfo(:
// Multiple registrations
void RegisterCompiledInInfo(const TCHAR* PackageName, const FClassRegisterCompiledInInfo* ClassInfo, size_t NumClassInfo, const FStructRegisterCompiledInInfo* StructInfo, size_t NumStructInfo, const FEnumRegisterCompiledInInfo* EnumInfo, size_t NumEnumInfo)
{
	LLM_SCOPE(ELLMTag::UObject);
	LLM_SCOPE_BYTAG(UObject_UObjectBase);

	for (size_t Index = 0; Index < NumClassInfo; ++Index)
	{
		const FClassRegisterCompiledInInfo& Info = ClassInfo[Index];
		RegisterCompiledInInfo(Info.OuterRegister, Info.InnerRegister, PackageName, Info.Name, *Info.Info, Info.VersionInfo);
	}

	for (size_t Index = 0; Index < NumStructInfo; ++Index)
	{
		const FStructRegisterCompiledInInfo& Info = StructInfo[Index];
		RegisterCompiledInInfo(Info.OuterRegister, PackageName, Info.Name, *Info.Info, Info.VersionInfo);
		if (Info.CreateCppStructOps != nullptr)
		{
			UScriptStruct::DeferCppStructOps(FTopLevelAssetPath(FName(PackageName), FName(Info.Name)), (UScriptStruct::ICppStructOps*)Info.CreateCppStructOps());
		}
	}

	for (size_t Index = 0; Index < NumEnumInfo; ++Index)
	{
		const FEnumRegisterCompiledInInfo& Info = EnumInfo[Index];
		RegisterCompiledInInfo(Info.OuterRegister, PackageName, Info.Name, *Info.Info, Info.VersionInfo);
	}
}
```

<br>

```cpp  File:UObjectGlobals.cpp hlt:6
#if !UE_WITH_CONSTINIT_UOBJECT
void RegisterCompiledInInfo(class UClass* (*InOuterRegister)(), class UClass* (*InInnerRegister)(), const TCHAR* InPackageName, const TCHAR* InName, FClassRegistrationInfo& InInfo, const FClassReloadVersionInfo& InVersionInfo)
{
	check(InOuterRegister);
	check(InInnerRegister);
	FClassDeferredRegistry::AddResult result = FClassDeferredRegistry::Get().AddRegistration(InOuterRegister, InInnerRegister, InPackageName, InName, InInfo, InVersionInfo);
#if WITH_RELOAD
	if (result == FClassDeferredRegistry::AddResult::ExistingChanged && !IsReloadActive())
	{
		// Class exists, this can only happen during hot-reload or live coding
		UE_LOG(LogUObjectBase, Fatal, TEXT("Trying to recreate changed class '%s' outside of hot reload and live coding!"), InName);
	}
#endif
	FString NoPrefix(UObjectBase::RemoveClassPrefix(InName));
	NotifyRegistrationEvent(InPackageName, *NoPrefix, ENotifyRegistrationType::NRT_Class, ENotifyRegistrationPhase::NRP_Added, (UObject * (*)())(InOuterRegister), false);
	NotifyRegistrationEvent(InPackageName, *(FString(DEFAULT_OBJECT_PREFIX) + NoPrefix), ENotifyRegistrationType::NRT_ClassCDO, ENotifyRegistrationPhase::NRP_Added, (UObject * (*)())(InOuterRegister), false);
}
```

<br>


<br>

```cpp file:DeferredRegistry.h
//여기에 저장된 놈들이 reflection시스템에 등록되어 사용
// 사실 어떤 의미인지 잘 모르겠음
using FClassDeferredRegistry = TDeferredRegistry<FClassRegistrationInfo>;
using FEnumDeferredRegistry = TDeferredRegistry<FEnumRegistrationInfo>;
using FStructDeferredRegistry = TDeferredRegistry<FStructRegistrationInfo>;
using FPackageDeferredRegistry = TDeferredRegistry<FPackageRegistrationInfo>;
// ********** End Registration *********************************************************************
```


<br>

## 실제 UFUCTION을 만드는 과정

```cpp file:TestActor.gen.cpp hl:5
UClass* Z_Construct_UClass_ATestActor()
{
	if (!Z_Registration_Info_UClass_ATestActor.OuterSingleton)
	{
		UECodeGen_Private::ConstructUClass(Z_Registration_Info_UClass_ATestActor.OuterSingleton, Z_Construct_UClass_ATestActor_Statics::ClassParams);
	}
	return Z_Registration_Info_UClass_ATestActor.OuterSingleton;
}
```
<br>


```cpp file:UObjectGlobal.h	hl:3
void ConstructUClass(UClass*& OutClass, const FClassParams& Params)
	{
		ConstructUClassHelper<UClass>(OutClass, Params, [](UClass*, const FClassParams&) {});
	}
```

<br>

```cpp file:UObjectConstructInternal.h ln:3 hl:8
template<typename UClassClass, typename ClassParams, typename PostNewFn>
void ConstructUClassHelper(UClass*& OutClass, const ClassParams& Params, PostNewFn&& InPostNewFn)
{
	~
	
	NewClass->CreateLinkAndAddChildFunctionsToMap(Params.FunctionLinkArray, Params.NumFunctions);
	
	~
}
```
<br>


```cpp hlt:Functions->Crea: file:Class.cpp 
void UClass::CreateLinkAndAddChildFunctionsToMap(const FClassFunctionLinkInfo* Functions, uint32 NumFunctions)
{
	for (; NumFunctions; --NumFunctions, ++Functions)
	{
		const char* FuncNameUTF8 = Functions->FuncNameUTF8;
		UFunction*  Func         = Functions->CreateFuncPtr();

		Func->Next = Children;
		Children = Func;

		AddFunctionToFunctionMap(Func, FName((const UTF8CHAR*)FuncNameUTF8));
	}
}
```

<br>

```cpp file:Class.h hlt:UF:
struct FClassFunctionLinkInfo
{
	UFunction* (*CreateFuncPtr)();
	const char* FuncNameUTF8;
};
```

<br>

```cpp file:TestActor.gen.cpp hl:6
UFunction* Z_Construct_UFunction_ATestActor_Test_ImplementableFunc()
{
	static UFunction* ReturnFunction = nullptr;
	if (!ReturnFunction)
	{
		UECodeGen_Private::ConstructUFunction(&ReturnFunction, Z_Construct_UFunction_ATestActor_Test_ImplementableFunc_Statics::FuncParams);
	}
	return ReturnFunction;
}
```
<br>


```cpp file:UObjectGlobal.cpp hl:1
	void ConstructUFunction(UFunction** SingletonPtr, const FFunctionParams& Params)
	{
		ConstructUFunctionHelper<UFunction>(*SingletonPtr, Params, SingletonPtr,
			[](UObject* Outer, UFunction* Super, FName FuncName, const FFunctionParams& Params) -> UFunction*
			{
				return new (EC_InternalUseOnlyConstructor, Outer, FuncName, Params.ObjectFlags) UFunction(
					FObjectInitializer(),
					Super,
					Params.FunctionFlags,
					Params.StructureSize
				);
			});
	}
```
