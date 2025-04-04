### <span style="background:lightgray">PrinterManager.PrintImage()</span>

```csharp
public async void PrintImage(string filePath)
{
	printFilePath = filePath;
	// 프린터 출력 작업을 위한 기본 클래스 생성
	PrintDocument printDocument = new PrintDocument();
	// 사이즈 지정 : 4x6 (= 400x600)
	printDocument.DefaultPageSettings.PaperSize = new PaperSize("4x6", 400, 600);
	// 전체 페이지를 다 쓰기 위해 여백 제거
	printDocument.DefaultPageSettings.Margins = new Margins(0, 0, 0, 0);
	
	printDocument.PrintPage += PrintPage; // Add event handler

	try
	{
		// 프린트 작업을 비동기로 실행
		await Task.Run(() => printDocument.Print());
		Debug.Log("Print Start");
	} 
	catch (Exception e)
	{
		Debug.LogError("An error occurred while printing: " + e.Message);
	}
}
```

