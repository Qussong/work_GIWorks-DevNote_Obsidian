
반환값이 없는 메서드를 참조할 수 있는 미리 정의된 델리게이트 타입으로, 간단한 콜백을 처리하는 데 적합하다.
### 특징

1. 매개변수 없는 메서드를 참조할 때 사용된다.
	```csharp
	Action action = () => Console.WriteLine("Action 호출!");
	action.Invoke(); // 출력: Action 호출!
	```
2. Action<T\> 형식은 하나 이상의 매개변수를 받을 수 있다. (최대 15개)
	```csharp
	Action<int> printNumber = (num) => Console.WriteLine($"Number: {num}");
	printNumber(42); // 출력: Number: 42
	```
3. System 네임스페이스에 정의되어있다.

### Action의 시그니처
| 타입             | 설명                                           |
| -------------- | -------------------------------------------- |
| Action         | 파라미터가 없고 반환값도 없는 메서드 참조                      |
| Action<T\>     | 하나의 파라미터를 받고 반환값이 없는 메서드 참조                  |
| Action<T1, T2> | 두 개의 파라미터를 받고 반환값이 없는 메서드 참조                 |
| ...            | 최대 Action<T1,...,T16> 까지 16개의 매개변수를 받을 수 있음. |

### 주 활용처
1. 콜백 함수
	작업 완료 후 호출할 동작을 정의할 때 활용한다.
2. 간단한 이벤트 처리
	버튼 클릭 등 특정 작업에 대한 동작을 정의
	```csharp
	public class Button
	{
	    public event Action OnClick;
	
	    public void Click()
	    {
	        OnClick?.Invoke(); // 이벤트 발생
	    }
	}
	
	// 사용
	Button button = new Button();
	button.OnClick += () => Console.WriteLine("Button clicked!");
	button.Click(); // 출력: Button clicked!
	```
3. 유연한 함수 참조
	메서드를 동적으로 연결해 실행할 때 사용한다.

### Action 과 Delegate 의 차이

Action은 델리게이트와 유사하지만, **미리 정의된 델리게이트 타입**이라 더 간결하고 표준화된 형태를 제공한다.

| 구분  | Delegate                                      | Action             |
| :-: | --------------------------------------------- | ------------------ |
| 정의  | 직접 선언<br>`public delegate void MyDelegate();` | 사전 정의된 타입으로 선언 불필요 |
| 반환값 | **사용자 지정 가능**                                 | **항상 void**        |
| 사용성 | 메서드 참조와 타입 선언을 모두 정의해야 함                      | 간편하게 재사용 가능        |
Action 과 Delegate 중 무엇을 사용할지는 주로 **반환값의 존재 여부**가 중요한 기준이 된다.

