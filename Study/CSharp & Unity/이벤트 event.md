
### 이벤트란?

특정 동작이 발생했을 때 실행되는 **알림 시스템**이라고 할 수 있다.
버튼이 클릭되었을 때 알림을 주거나, 페이지가 전환되었을 때 어떤 작업을 실행하도록 한다.
발행자와 구독자라는 역할이 존재하며 각각은 아래와 같은 역할을 수행한다.
- 발행자 : 이벤트를 선언하고, 발생시키는 클래스이다.
- 구독자 : 이벤트가 발생했을 때 실행될 동작(메서드)을 정의하고, 이벤트를 구독한다.


- C# 에서 이벤트를 정의할 때 사용하는 키워드로, 객체 간의 **비동기적인 통신**을 가능하게 한다.
- 이벤트는 **델리게이트**를 기반으로 동작하며, 주로 **Observer 패턴**을 구현하는 데 사용된다.
- 이벤트는 데이터를 발행하는 **발행자**와 이를 처리하는 **구독자**간의 관계를 표현한다.

### 정의 및 구현

```csharp
public class UIManager
{
    // 이벤트 선언
    public event Action OnMoveHome;

    // 메서드에서 이벤트 호출
    public void MoveHome()
    {
        Console.WriteLine("Moving to Home...");
        OnMoveHome?.Invoke(); // 이벤트 호출 (구독자에게 알림)
    }
}

// 이벤트 구독 및 사용
UIManager uiManager = new UIManager();
uiManager.OnMoveHome += () => Console.WriteLine("Home 이동 이벤트 발생!");
uiManager.MoveHome();
```