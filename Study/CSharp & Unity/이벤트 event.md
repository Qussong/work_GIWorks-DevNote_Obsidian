
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