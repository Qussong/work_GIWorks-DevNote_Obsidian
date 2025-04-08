
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
    public event EventHandler<DataEventArgs> ProcessCompleted;
    public void Process(string data)
    {
        Console.WriteLine("데이터를 처리 중...");
        string result = $"[{data.ToUpper()}] 처리완료";
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
        DataProcessor pro = new DataProcessor();
        Subscriber sub = new Subscriber();

        pro.ProcessCompleted += sub.OnProcessCompleted;
        pro.Process("example data");
    }
}
```

### EventHandler 와 EventHandler<T\>
C#에서는 기본적으로 제공되는 이벤트 처리용 델리게이트 두 가지가 있다.
1. EventHandler
	**데이터가 필요 없는** 일반적인 이벤트 처리

2. EventHandler<T\>
	**추가적인 데이터를 포함**하는 이벤트 처리


