
- 복잡한 이벤트를 처리하는 데 사용된다.
- 이벤트와 함께 추가적인 데이터를 전달할 때 사용한다.

### 정의 및 구현

```csharp
// 이벤트 데이터 클래스
public class PageChangedEventArgs : EventArgs
{
    public string PreviousPage { get; set; }
    public string CurrentPage { get; set; }
}

public class UIManager
{
    // EventHandler<T> 이벤트 선언
    public event EventHandler<PageChangedEventArgs> OnPageChanged;

    // 메서드에서 이벤트 호출
    public void ChangePage(string previousPage, string currentPage)
    {
        Console.WriteLine($"Changing page from {previousPage} to {currentPage}...");
        OnPageChanged?.Invoke(this, new PageChangedEventArgs
        {
            PreviousPage = previousPage,
            CurrentPage = currentPage
        });
    }
}

// 이벤트 구독 및 사용
UIManager uiManager = new UIManager();
uiManager.OnPageChanged += (sender, args) => 
    Console.WriteLine($"이전 페이지: {args.PreviousPage}, 현재 페이지: {args.CurrentPage}");
uiManager.ChangePage("Home", "Settings");
```