>**이벤트는 델리게이트 기반으로 만들어졌지만, 특정 작업 후 구독자에게 알림을 주는 데 특화되어 있다**.
### 이벤트란?
- 이벤트는 특정 동작이 발생했을 때, 미리 등록된 메서드를 호출하도록 설계된다.
- C# 에서 이벤트를 정의할 때 사용하는 키워드로, 객체 간의 **비동기적인 통신**을 가능하게 한다.
- 이벤트는 **델리게이트**를 기반으로 동작하며, 주로 **Observer 패턴**을 구현하는 데 사용된다.
- 발행자와 구독자라는 역할이 존재하며 각각은 아래와 같은 역할을 수행한다.
	1. 발행자 (publisher) : 이벤트를 선언하고, 발생시키는 클래스이다.
	2. 구독자 (subscriber) : 이벤트가 발생했을 때 실행될 동작(메서드)을 정의하고, 이벤트를 구독한다.

### 특징
1. 델리게이트 기반
	이벤트는 델리게이트를 기반으로 동작한다.
	델리게이트는 이벤트가 호출될 때 실행할 메서드를 참조한다.

2. Observer 패턴 구현
	객체들 간의 느슨한 결합을 제공한다.
	이벤트를 통해 특정 상태 변화에 대한 알림을 보낼 수 있다.

3. 구독/발행 모델
	여러 메서드를 구독할 수 있다.
	발행자는 구독자에게 알림을 발행한다.


### 선언 및 사용법
이벤트는 event 키워드를 사용해 선언된다.
```csharp
public class Publisher
{
    public event Action OnProcessCompleted;

    public void Process()
    {
        Console.WriteLine("작업 실행 중...");

        OnProcessCompleted?.Invoke();   // 구독자에게 알림
    }
}

public class Subscriber
{
    public void HandleEvent()
    {
        Console.WriteLine("구독자 : 작업 완료 알림 받음!");
    }
}

class Program
{
    static void Main(string[] args)
    {
        Publisher pub = new Publisher();
        Subscriber sub = new Subscriber();
        pub.OnProcessCompleted += sub.HandleEvent;

        pub.Process();
        // 작업 실행 중...
        // 구독자 : 작업 완료 알림 받음!
    }
}
```

### 주요 구성요소

1. **`event` 키워드**
	이벤트를 정의할 때 사용하며, 델리게이트를 기반으로 동작한다.
	이벤트는 델리게이트처럼 동작하지만, **발생자 외에는 호출이 불가능**하다.

2. **구독(Add) 및 해지(Remove)**
	구독자는 `+=` 연산자를 사용해 이벤트에 메서드를 등록하고, `-=` 연산자로 이벤트 구독을 해지할 수 있다.

3. **null 체크(`?.Invoke()`)**
	이벤트를 호출하기 전에 구독자가 있는지 확인하는 것이 중요하다.


### Event vs Delegate

| 특징   | 이벤트                                       | 델리게이트              |
| ---- | ----------------------------------------- | ------------------ |
| 기반   | 델리게이트를 기반으로 동작                            | 특정 메서드 참조          |
| 접근제한 | 이벤트 발생자는 호출 가능(`?.Invoke()`)<br>외부는 호출 불가 | 외부에서도 호출 가능        |
| 사용목적 | 구독/발행 모델.<br>여러 구독자에게 알림                  | 메서드를 동적으로 호출하거나 연결 |

### 이벤트와 Action 의 결합
이벤트는 Action 과 쉽게 결합할 수 있다.
```csharp
public class Publisher
{
    public event Action<string> OnMessage;
    public void SendMsg(string msg)
    {
        OnMessage?.Invoke(msg);
    }
}

class Program
{
    static void Main(string[] args)
    {
        Publisher pub = new Publisher();
        pub.OnMessage += (msg) => Console.WriteLine($"알림 : {msg}");
        pub.SendMsg("Hello, World");  // 알림 : Hello, World
    }
}
```

### ❗event + Func
Func 의 특성상 반환값이 필요하기에 이벤트와 결합하는 경우 구독자들이 반환값을 제공하는 형태로 동작한다.
Func의 마지막 타입은 항상 반환값을 나타낸다. (`Func<T1,T2,...,TResult>`)
```csharp
public class Publisher
{
    public event Func<int, string> OnRequestData;

    public void RequestData(int id)
    {
        if(null != OnRequestData)
        {
            foreach(Func<int,string> handler in OnRequestData.GetInvocationList())
            {
                string result = handler.Invoke(id); // 구독자 호출
                Console.WriteLine($"구독자가 반환한 데이터 => {result}");
            }
        }
    }
}

public class Subscriber
{
    public string ProvideData(int value)
    {
        return $"데이터 처리 완료 : {value + 10}";
    }
}

class Program
{
    static void Main(string[] args)
    {
        Publisher pub = new Publisher();
        Subscriber sub = new Subscriber();
        // 이벤트 구독
        pub.OnRequestData += sub.ProvideData;
        // 요청 실행행
        pub.RequestData(40);
        // 출력값
        // 구독자가 반환한 데이터 => 데이터 처리 완료 : 50
    }
}
```
