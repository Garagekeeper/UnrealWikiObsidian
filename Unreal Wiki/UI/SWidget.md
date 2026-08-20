# SWidget
Swidget 은 UE에서 제공하는 Frame Work인 [Slate](https://dev.epicgames.com/documentation/unreal-engine/slate-overview-for-unreal-engine)에서 가장 기본이 되는 객체이다. UWidget이 아니기 때문에 GC를 사용할 수 없어서 내부적으로 스마트 포인터를 사용한다. 언리얼 엔진 에디터도 Slate를 사용해서 제작되었으며 우리가 사용하는 Widget Reflector또한 Slate기반으로 작동한다. 그렇다면 SWidget은 UWidget과 어떤 관계일까? 몇가지 자료들을 살펴본 결과 UWidget은 SWidget을 BluePrint 혹은 에디터상에서 사용할 수 있도록 해주는 래퍼클래스라고 생각하면 될것같다. Slate는 순수 c++을 사용해서 UI를 그려주기 때문에, 이를 GUI를 통해서 생성 조작하기 위해서 UWdiget이라는 형태가 탄생한 것이다. 실제로 UWidget은 내부에 SWidget을 변수로 들고 있으며, UWiget을 변경하면 변경내용을 SWidget으로 그대로 넘겨주는 코드도 많다.

여태까지의 내용은 그다지 흥미롭지 않을 수 있다. 하지만 내가 공부하면서 알아낸것들 중에서 가장 흥미로운 내용은 에디터를 내가 원하는대로 수정할 수 있다는 점이다. 원하는 위치에 원하는 UI를 배치하고 기능을 연결한다면 '마개조'라 칭하는 일들을 할 수 있을지도 모른다.

```cpp file:Widget.h
protected:
	// UWidget의 내부에서 SWidGet을 가지고 있다.
	/** The underlying SWidget. */
	TWeakPtr<SWidget> MyWidget;

	/** The underlying SWidget for the Component wrapper widget. */
	TWeakPtr<SWidget> ComponentWrapperWidget;
```

```cpp file:UImge.cpp
// SynchronizeProperties에서는 UImage에서 변화된 값들을 토대로 SImage를 변경해준다. 
// 여기서 SImga가 실제 우리 눈에 보이는 위젯이다.

// 에디터에서 값의 변화를 바로 적용하고 싶을때 SynchronizeProperties를 오버라이드해서 사용했는데 
// 바로 이거다.
void UImage::SynchronizeProperties()
{
	Super::SynchronizeProperties();

PRAGMA_DISABLE_DEPRECATION_WARNINGS
	TAttribute<FSlateColor> ColorAndOpacityBinding = PROPERTY_BINDING(FSlateColor, ColorAndOpacity);
	TAttribute<const FSlateBrush*> ImageBinding = OPTIONAL_BINDING_CONVERT(FSlateBrush, Brush, const FSlateBrush*, ConvertImage);
PRAGMA_ENABLE_DEPRECATION_WARNINGS
	if (MyImage.IsValid())
	{
		MyImage->SetImage(ImageBinding);
		MyImage->InvalidateImage();
		MyImage->SetColorAndOpacity(ColorAndOpacityBinding);
		MyImage->SetOnMouseButtonDown(BIND_UOBJECT_DELEGATE(FPointerEventHandler, HandleMouseButtonDown));
	}
}
```

[SWidget에 직관적인 이해를 얻을 수 있었던 글](https://create-frame.tistory.com/4)
