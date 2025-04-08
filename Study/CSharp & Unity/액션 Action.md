
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
```csharp
```