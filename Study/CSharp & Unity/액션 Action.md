
- 델리게이트의 한 유형으로, 매개변수가 없고 반환 값도 없는 메서드를 참조하는 데 사용된다.
- 미리 정의된 델리게이트 타입으로, 간단한 콜백을 처리하는 데 적합하다.

### 특징

1. 매개변수 없는 메서드를 참조할 때 사용된다.
	```csharp
	Action action = () => Console.WriteLine("Action 호출!");
	action.Invoke(); // 출력: Action 호출!
	```
2. Action<T\> 형식은 하나 이상의 매개변수를 받을 수 있다.
	```csharp
	Action<int> printNumber = (num) => Console.WriteLine($"Number: {num}");
	printNumber(42); // 출력: Number: 42
	```
