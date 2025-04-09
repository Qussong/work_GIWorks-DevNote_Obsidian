
- 클래스나 구조체가 구현해야 할 **메서드**, **속성**, **이벤트** 및 기타 멤버들의 계약을 정의하는 데 사용된다.
- 구현 세부 사항을 포함하지 않으며, 오직 멤버들의 **시그니처**만을 정의한다. 즉, 인터페이스는 "어떤 기능을 제공해야 한다"라는 **약속**을 나타냅니다.

### 특징
1. 구현 강제 : 인터페이스를 상속한 클래스는 **인터페이스에 정의된 모든 멤버를 구현**해야한다.
2. 다중 상속 지원 : C#에서 클래스는 다중 상속을 지원하지 않지만, 인터페이스는 **다중 구현**이 가능하다.
3. 추상성 : 인터페이스는 구현을 제공하지 않기에 **코드의 추상화**를 도와준다.
4. 유연성 : 인터페이스를 통해 서로 다른 클래스들이 **공통된 동작**을 제공하도록 할 수 있다.

### 정의 및 구현
1. 인터페이스 멤버 구현의 의무
	- 클래스나 구조체는 인터페이스에 정의된 모든 멤버를 반드시 구현해야한다.
	- 구현하지 않으면 해당 클래스나 구조체는 **추상 클래스**로 간주되며 인스턴스를 생성할 수 없다.
	```csharp
	public interface IExample
	{
	    void PrintMessage(); // 메서드 정의
	}
	
	public class Example : IExample
	{
	    public void PrintMessage() // 반드시 구현해야 함
	    {
	        Console.WriteLine("Hello, world!");
	    }
	}
	```
2. 모든 멤버는 기본적으로 public
	- 인터페이스의 멤버는 기본적으로 public 접근 제한자를 가진다. 때문에 명시적으로 public을 사용할 필요가 없다.
	- 인터페이스를 상속한 클래스에서는 반드시 public 으로 구현해야한다.
	```csharp
	public interface IExample
	{
	    void Display(); // public
	}
	
	public class Example : IExample
	{
	    public void Display() // public으로 구현해야 함
	    {
	        Console.WriteLine("Display method");
	    }
	}
	```
3. 다중 인터페이스 구현 가능
	- 클래스는 여러 개의 인터페이스를 동시에 구현할 수 있다. 이 경우, 각 인터페이스의 모든 멤버를 구현해야 한다.
	```csharp
	public interface IAnimal
	{
	    void Eat();
	}
	
	public interface IPlant
	{
	    void Grow();
	}
	
	public class Organism : IAnimal, IPlant
	{
	    public void Eat()
	    {
	        Console.WriteLine("Eating...");
	    }
	
	    public void Grow()
	    {
	        Console.WriteLine("Growing...");
	    }
	}
	```
4. 명시적 인터페이스 구현
	- 동일한 이름의 멤버가 여러 인터페이스에 정의되어 있는 경우, 명시적 구현을 사용하여 충돌을 방지할 수 있다.
	- 명시적 구현된 멤버는 인터페이스 타입을 통해서만 호출 가능
	```csharp
	public interface IA
	{
	    void DoWork();
	}
	
	public interface IB
	{
	    void DoWork();
	}
	
	public class Example : IA, IB
	{
	    void IA.DoWork()
	    {
	        Console.WriteLine("IA's DoWork");
	    }
	
	    void IB.DoWork()
	    {
	        Console.WriteLine("IB's DoWork");
	    }
	}
	
	// 사용 예
	IA a = new Example();
	a.DoWork(); // IA의 DoWork 실행
	
	IB b = new Example();
	b.DoWork(); // IB의 DoWork 실행
	```
5. 인터페이스 멤버는 구현 세부 사항을 포함하지 않음
	- 인터페이스는 구현이 없는 메서드, 속성, 이벤트 또는 인덱서를 정의한다.
	- 단, C# 8.0 이후부터 **디폴트 구현**을 지원하여 인터페이스에 기본 구현을 추가할 수 있다.
	```csharp
	public interface IExample
	{
	    void DoWork();
	
	    // 디폴트 구현 제공
	    void DefaultMethod()
	    {
	        Console.WriteLine("Default implementation.");
	    }
	}
	
	public class Example : IExample
	{
	    public void DoWork()
	    {
	        Console.WriteLine("Work is being done!");
	    }
	}
	```
6. 필드를 가질 수 없음
	- 인터페이스에는 필드를 정의할 수 없습니다. 필드는 클래스나 구조체에서만 사용할 수 있다.
	- 인터페이스는 오직 메서드, 속성, 이벤트, 인덱서만 정의할 수 있다.
7. 인터페이스 상속
	- 인터페이스는 다른 인터페이스를 상속할 수 있으며, 다중 상속도 가능하다.
	```csharp
	public interface IBase
	{
	    void BaseMethod();
	}
	
	public interface IDerived : IBase
	{
	    void DerivedMethod();
	}
	
	public class Example : IDerived
	{
	    public void BaseMethod()
	    {
	        Console.WriteLine("BaseMethod implementation.");
	    }
	
	    public void DerivedMethod()
	    {
	        Console.WriteLine("DerivedMethod implementation.");
	    }
	}
	```
8. 인터페이스를 사용하는 객체 간의 관계
	- 인터페이스를 사용하면 **객체 간의 느슨한 결합**을 유지할 수 있습니다.
	- 이는 의존성 역전(DI) 및 SOLID 원칙을 준수하는 데 유리하다.

### 느슨한 결합 loose coupling

소프트웨어 설계에서 객체들이 서로 최소한의 의존성을 가지면서 협력하도록 만드는 것을 의미한다. 객체들이 서로에 대해 구체적인 정보를 거의 알지 못하도록 설계하는 방식으로, 코드의 유연성과 유지보수성을 높이는 데 중요한 개념이다.

1. 독립성 증가
2. 확장성 향상
3. 유지보수성 강화

```csharp
public interface IMessageSender
{
    void SendMessage(string message);
}

public class EmailSender : IMessageSender
{
    public void SendMessage(string message)
    {
        Console.WriteLine($"Email: {message}");
    }
}

public class Notification
{
    private readonly IMessageSender _messageSender;

    public Notification(IMessageSender messageSender)
    {
        _messageSender = messageSender;
    }

    public void Notify(string message)
    {
        _messageSender.SendMessage(message);
    }
}

// 사용
IMessageSender sender = new EmailSender();
Notification notification = new Notification(sender);
notification.Notify("안녕하세요!");
```
