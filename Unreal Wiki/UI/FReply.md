# [FReply](https://dev.epicgames.com/documentation/unreal-engine/API/Runtime/SlateCore/FReply)

FReply는 Slate 이벤트가 반환하는 것으로 단순히 값을 반환하는것이 아니라 시스템에게 이벤트가 어떻게 처리되었는지 알려주는 구조체이다.
`FReply::Handled()`, `FReply::UnHandled()` 과 같은 함수를 통해서 현재 위젯이 이벤트를 처리했는지, 통과 시키는지 시스템에 알려준다. 또한 `.DetectDrag(...)`를 통해서 이 입력이 드래그로 이어지는 Slate에게 탐지 요청을 할 수도 있다.
```cpp file:FReply_예시 hlt:10
FReply UInventorySlotWidget::NativeOnMouseButtonDown(const FGeometry& InGeometry, const FPointerEvent& InMouseEvent)
{
	if (InMouseEvent.IsMouseButtonDown(EKeys::LeftMouseButton))
	{
		if (FInventorySlot* InvenSlot = TargetInventory->GetSlot(Index))
		{
			if (InvenSlot->IsEmpty()) 
				return Super::NativeOnMouseButtonDown(InGeometry, InMouseEvent);

			return FReply::Handled().DetectDrag(TakeWidget(), EKeys::LeftMouseButton);
		}
	}
	return Super::NativeOnMouseButtonDown(InGeometry, InMouseEvent);
}

```