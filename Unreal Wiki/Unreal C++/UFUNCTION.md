# UFUNCTION
UFUNCTION은 함수를 Reflection System에 등록하기 위한 매크로다.
프고젝트가 컴파일 될 때 [[UHT(Unreal Header Tool)]]가 이 매크로를 파싱해서 이 함수에 필요한 코드들을 생성해준다. 이후에 UFUNCTION 자체는 비워지지만 `.generated.h`에 함수의 Specifier에 따라 `DECLARE_FUNCTION(execXXX)`, RPC 함수, 이벤트 함수 등의 코드가 생성된다.

```C#
// 매크로 안보여서 C#으로 코드블록 만들었지만 사실 C++ 코드
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

* Keyword
	* **BlueprintCallable**: cpp에서 작성한 코드를 BP에서 호출 가능
	* **BlueprintImplementableEvent**: cpp에서 선언만, BP에서 구현
		* 커스텀 이벤트로 만들면 안되고 반드시 오버라이드에서 뽑아 써야함
		*  .gen.cpp에서 구현해주는 형태
	* **BlueprintNativeEvent**: cpp에서 선언 및 구현 가능, BP에서 재정의 가능
		* cpp에서 함수 이름 뒤에 \_Implementation을 붙여서 구현 해야함.
		* 커스텀 이벤트로 만들면 안되고 반드시 오버라이드에서 뽑아 써야함
```cpp
	UFUNCTION(BlueprintNativeEvent)
	void Attack();
		  
	void APlayer::Attack_Implementation() { }
```

* **BlueprintPure**: 실행핀 없이 호출되는 함수(순서에 상관 없이)(BP에서 녹색으로 표시되는 노드들)
	* Get 계열 함수에 적합

```cpp
// ********** Begin Class ATestActor Function Test_ImplementableFunc *******************************

//BlueprintImplementableEvent 키워드 함수의 생성코드

static FName NAME_ATestActor_Test_ImplementableFunc = FName(TEXT("Test_ImplementableFunc"));

void ATestActor::Test_ImplementableFunc()
{
    UFunction* Func = FindFunctionChecked(NAME_ATestActor_Test_ImplementableFunc);
    ProcessEvent(Func,NULL);
}

struct Z_Construct_UFunction_ATestActor_Test_ImplementableFunc_Statics
{
#if WITH_METADATA
    static constexpr UECodeGen_Private::FMetaDataPairParam Function_MetaDataParams[] = {

        { "ModuleRelativePath", "Public/TestActor.h" },
    };

#endif // WITH_METADATA
// ********** Begin Function Test_ImplementableFunc constinit property declarations ****************
// ********** End Function Test_ImplementableFunc constinit property declarations ******************

    static const UECodeGen_Private::FFunctionParams FuncParams;
};

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