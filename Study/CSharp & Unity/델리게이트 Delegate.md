
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


```