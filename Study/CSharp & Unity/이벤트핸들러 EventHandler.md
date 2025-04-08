
- 이벤트 핸들러는 이벤트가 발생했을 때 실행되는 메서드다.
- 이벤트를 구독하는 메서드 또는 람다가 이벤트 핸들러의 역할을 한다.
- 핸들러는 특정 이벤트를 처리하고, 그에 따라 원하는 동작을 실행한다.

### 기본 구조
C#에서 이벤트 핸들러를 작성하려면 다음 규칙을 따라야 한다.
1. 이벤트 핸들러는 **델리게이트 타입**으로 정의된다.
2. 일반적으로 `EventHandler` 또는 `EventHandler<T>`를 사용해 표준화된 이벤트 처리 방식을 구현한다.
```csharp
public delegate void EventHandler(object sender, EventArgs e);
// object sender : 이벤트를 발생시킨 객체 (발행자)
// EventArgs e : 추가적인 이벤트 데이터를 포함하는 객체 (없을 경우 기본값으로 EventArgs.Empty 사용)
```

