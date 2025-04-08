fmf 
메서드를 참조하는 타입으로, 이를 통해 메서드를 변수처럼 다룰 수 있다.
델리게이트는 특히 이벤트, 콜백, 다형성 구현에 유용하게 활용된다.

### 델리게이트의 기초
특정 메서드의 **시그니처**(반환타입과 매개변수)와 일치하는 메서드만 참조할 수 있다.
`delegate` 키워드를 사용해 정의하며, 이 **정의에 맞는 메서드만 연결**할 수 있다.

```csharp
// 델리게이트 선언
public delegate void MyDelegate(string message);

// 델리게이트 사용 예
public class Example
{
    public void PrintMessage(string message)
    {
        Console.WriteLine(message);
    }
}

class Program
{
    static void Main(string[] args)
    {
        // 델리게이트 사용
        Example example = new Example();
        MyDelegate del = new MyDelegate(example.PrintMessage);
        del("Hello, Delegate!");
    }
}
```

### 문법
1. 델리게이트 선언
```csharp
public delegate [반환형] [델리게이트명]([매개변수 리스트]);

// ex
public delegate int Calculate(int x, int y);
```

2. 델리게이트 인스턴스 생성
❓델리게이트는 선언된 메서드에 연결해야 한다. 
```csharp
Calculate calc = new Calculate(Add);
```

3. 델리게이트 호출
델리게이트를 호출하면 연결된 메서드가 실행된다.
```csharp
int result = calc(10, 20);  // Add 메서드가 실행된다.
```

```csharp
public delegate void Calculate(int x, int y);

class Program
{
    static void Main(string[] args)
    {
        Calculate calc = new Calculate((a, b) => Console.WriteLine(a+b));
        calc(1,2);  // 
    }
}
```

### 접근 제한자

C#에서 델리게이트 타입을 선언할 때 반드시 public 접근 제한자를 사용해야 하는 것은 아니다. 접근 제한자를 생략하면 기본적으로 `internal`로 설정된다.
- public : 모든 곳에서 델리게이트를 사용할 수 있다.
- **internal** (기본값) : 같은 어셈블리 내에서만 델리게이트를 사용할 수 있다.
	> 어셈블리란 .NET 애플리케이션의 기본 빌드 단위를 의미한다. 
	> "같은 어셈블리"란, 같은 컴파일 결과물에 속하는 코드들끼리 포함된 범위를 의미한다. 즉, 같은 `.exe` 나 `.dll` 파일 내에서만 접근 가능한 코드를 의미한다.
- private : 델리게이트를 선언된 클래스 내부에서만 사용할 수 있다.


### 특징
1. **타입 안전성**
	델리게이트는 특정 반환형과 매개변수를 가진 메서드만 참조할 수 있어, 타입 안정성을 제공한다.
2. **멀티캐스트 지원**
	델리게이트는 여러 메서드를 참조할 수 있다.
	- `-=` : 델리게이트에서 메서드 제거
	- `+=` : 델리게이트에서 메서드 추가
```csharp
public delegate void Notify();
public class Example
{
    public void PrintHello() { Console.Write("Hello, "); }
    public void PrintWorld() { Console.Write("World"); }
}

class Program
{
    static void Main(string[] args)
    {
        Example example = new Example();
        Notify noti = new Notify(example.PrintHello);
        noti += example.PrintWorld;
		noti();  // Hello, World
    }
}
```
3. **함수형 프로그래밍 스타일**
	델리게이트는 **람다**와 함께 사용할 때 더욱 간결하고 유연하다.
```csharp
public delegate int Calculator(int x, int y);

class Program
{
    static void Main(string[] args)
    {
        Calculator calc = (x, y) => x + y;
        Calculator calc2 = new Calculator((x, y) => x + y);
        Console.WriteLine($"calc(1,2) = {calc(1,2)}");
        Console.WriteLine($"calc2(1,2) = {calc2(1,2)}");
        // 출력 : 
        // calc(1,2) = 3
        // calc2(1,2) = 3
    }
}
```
> `Calculator calc = (x, y) => x + y;` 과 `Calculator calc2 = new Calculator((x, y) => x + y);` 이 두 방식은 동일한 역할을 하며, 최종적으로 동일한 델리게이트 인스턴스를 생성한다. 그러나 현대적인 C# 코드에서는 첫번째 방식인 람다 식을 사용한 간결한 스타일이 더 일반적으로 사용된다.
> 명시적 객체 생성이 필요하거나, 코드 스타일에서 명확함을 중시하는 경우 두번재 방식을 선택할 수 있다.

### Func, Action, Predicate
1. Func
	- **반환값이 있는** 메서드를 참조한다.
	- 최대 16개의 매개변수를 받을 수 있다.
	- 꺽쇠의 마지막은 **반환값**을 의미한다. (`Func<T1, T2, ..., TResult>`)
```csharp
Func<int, int, int> add = (x,y) => x + y;
int result = add(10,20);  // 30
```
2. Action
	- **반환값이 없는** 메서드를 참조한다.
	- 최대 16개의 매개변수를 받을 수 있다.
```csharp
Action<string> print  = message => Console.WriteLine(message);
print("Hello");  // Hello
```
3. Predicate
	- **반환값이 bool**인 메서드를 참조한다.
```csharp
Predicate<int> evenCheck = x => x % 2 == 0;
bool isEven = evenCheck(4);  // True
```

### 콜백함수 (Callback)
- 특정 작업이 끝난 후 실행할 작업 지시(호출될 함수)를 전달하기 위한 방법이다.
- C#에서는 델리게이트를 사용하여 콜백 함수를 구현한다. 델리게이트는 특정 메서드의 참조를 저장하는 타입이다.
```csharp
public delegate void Callback(string result);
public class Example
{
    public void Execute(Callback callback)
    {
        // 작업 수행
        string result = "Task Completed";
        // 작업 완료 후 콜백 호출
        callback(result);
    }
}

class Program
{
    static void Main(string[] args)
    {
        Example example = new Example();
        example.Execute(result => Console.WriteLine(result)); // 출력: Task Completed
    }
}
```

