
- 이벤트 핸들러는 이벤트가 발생했을 때 실행되는 메서드다.
- 이벤트를 구독하는 메서드 또는 람다가 이벤트 핸들러의 역할을 한다.
- 핸들러는 특정 이벤트를 처리하고, 그에 따라 원하는 동작을 실행한다.

### 기본 개념
1. 이벤트
	- **무언가 발생했다** 를 알리는 신호
	- ex) 버튼 클릭, 파일 다운로드 완료, 데이터 로딩 등...
2. 이벤트 핸들러
	- **이벤트가 발생하면 실행되는 동작**을 정의하는 메서드
3. 발생자
	- 이벤트를 발생시키는 객체
	- ex) 버튼이나 특정 로직
4. 구독자
	- 이벤트에 반응하는 메서드를 가진 객체

### 람다식 활용
EventHandler 의 시그니처는 아래와 같다.
```csharp
void Handler(object sender, EventArgs e)
```
람다식을 통해 동일한 형태의 메서드를 간다히 정의하고 이벤트에 등록할 수 있다.
```csharp
public class Trigger
{
    public event EventHandler testHandler;
    public void Call()
    {
        testHandler?.Invoke(this, EventArgs.Empty);
    }
}

class Program
{
    static void Main(string[] args)
    {
        Trigger trigger = new Trigger();
        trigger.testHandler += (sender, e) => Console.Write("Trigger!");
        trigger.Call(); // Trigger!
    }
}
```

### 쉬운 예제 : 간단한 버튼 클릭 이벤트
코드 시나리오
1. 버튼을 누르면 이벤트가 발생한다.
2. 구독자가 버튼을 누른 후 어떤 메세지를 출력한다.
```csharp
public class Button
{
    public event EventHandler ButtonClicked;

    public void Click()
    {
        Console.WriteLine("버튼이 클릭되었습니다.");
        // 이벤트 발생
        ButtonClicked?.Invoke(this, EventArgs.Empty);   // 구독자에게 알림
    }
}

public class Subscriber
{
    public void OnButtonClicked(object sender, EventArgs e)
    {
        Console.WriteLine("구독자 : 버튼 클릭 이벤트 처리 중 ...");
    }
}

class Program
{
    static void Main(string[] args)
    {
        Button btn = new Button();
        Subscriber sub = new Subscriber();
        // 구독: 버튼 클릭 이벤트에 구독자 연결
        btn.ButtonClicked += sub.OnButtonClicked;
        // 버튼 클릭을 시뮬레이션
        btn.Click();
        // 출력물
        // 버튼이 클릭되었습니다.
        // 구독자 : 버튼 클릭 이벤트 처리 중 ...
    }
}
```

### 조금 더 복잡한 예제 : 데이터 전달 이벤트
코드 시나리오
1. 작업이 완료되면, 완료된 데이터를 이벤트를 통해 전달한다.
2. 구독자는 데이터를 받아 처리한다.
```csharp

using System.Reflection;

public class DataProcessor
{
    // 1. 이벤트 선언 (EventHandler<T> 사용용)
    public event EventHandler<DataEventArgs> ProcessCompleted;
    // 데이터를 처리하는 메서드드
    public void Process(string data)
    {
        Console.WriteLine("데이터를 처리 중...");
        string result = $"[{data.ToUpper()}] 처리완료";
        // 2. 이벤트 발생 (데이터와 함께 전달)
        ProcessCompleted?.Invoke(this, new DataEventArgs {ProcessedData = result });
    }
}

public class DataEventArgs : EventArgs
{
    public string ProcessedData {get; set;} // 추가 데이터
}

public class Subscriber
{
    // 이벤트 핸들러 메서드
    public void OnProcessCompleted(object sender, DataEventArgs e)
    {
        Console.WriteLine($"구독자 : 받은 데이터 - {e.ProcessedData}");
    }
}

class Program
{
    static void Main(string[] args)
    {
        DataProcessor pro = new DataProcessor();    // 데이터 처리 객체
        Subscriber sub = new Subscriber();  // 구독자 객체

        // 구독
        pro.ProcessCompleted += sub.OnProcessCompleted;
        // 데이터 처리 실행
        pro.Process("example data");
        // 출력물
        // 데이터를 처리 중...
        // 구독자 : 받은 데이터 - [EXAMPLE DATA] 처리완료
    }
}
```

### 커스텀 예제 (EventHandler<T\>)
```csharp
public class Publisher
{
    public event EventHandler<MyEventArgs> testEventHandler;

    public void CallEvent(MyEventArgs data)
    {
        testEventHandler?.Invoke(this, data);
    }
}

public class Subscriber
{
    public void HandlerFunc(object sender, MyEventArgs e)
    {
        Console.WriteLine($"Get Data : {e.SendData}");
    }
}

public class MyEventArgs : EventArgs
{
    public string SendData { get; set; }
}

class Program
{
    static void Main(string[] args)
    {
        Publisher pub = new Publisher();
        Subscriber sub = new Subscriber();

        pub.testEventHandler += sub.HandlerFunc;
        
        MyEventArgs data = new MyEventArgs() { SendData = "Hello" };
        pub.CallEvent(data);    // Get Data : Hello
    }
}
```
### EventHandler 와 EventHandler<T\>
C#에서는 기본적으로 제공되는 이벤트 처리용 델리게이트 두 가지가 있다.
1. EventHandler
	**데이터가 필요 없는** 일반적인 이벤트 처리

2. EventHandler<T\>
	**추가적인 데이터를 포함**하는 이벤트 처리

