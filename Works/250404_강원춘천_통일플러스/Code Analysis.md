### <span style="background:lightgray">PrinterManager.PrintImage()</span>
이미지 출력 기능을 비동기로 안정적으로 처리하는 기능
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
	// 인쇄할 내용을 정의하는 메서드(PringPage)를 이벤트에 연결
	printDocument.PrintPage += PrintPage; // Add event handler

	try
	{
		// 무거운 작업인 프린팅을 백그라운드 스레드에서 실행
		// UI 를 멈추지 않고 비동기적으로 진행됨
		// 프린트 작업을 비동기로 실행 
		await Task.Run(() => printDocument.Print());
		Debug.Log("Print Start");
	} 
	catch (Exception e)
	{
		// 프린트 중 문제가 생기면 에러 로그 남김
		Debug.LogError("An error occurred while printing: " + e.Message);
	}
}

// PrintPage 핸들러 이벤트가 호출되면 호출된 함수는 내부에서 Graphics.DrawImage() 와 같은 기능으로 이미지를 그려야 한다.
```

### <span style="background:lightgray">PrintPage</span>

```csharp
private void PrintPage(object sender, PrintPageEventArgs e)
{
	if (File.Exists(printFilePath))
	{
		try
		{
			// 이미지 파일 불러오기
			Image img = Image.FromFile(printFilePath);

			// 페이지 크기 (MarginBounds 대신 PageBounds 사용)
			RectangleF pageBounds = e.PageBounds;

			// 이미지가 페이지를 넘어갈 경우 축소 비율 계산
			if (img.Width > pageBounds.Width || img.Height > pageBounds.Height)
			{
				// 이미지 비율을 유지한 채 축소
				float scale = Math.Min(pageBounds.Width / img.Width, pageBounds.Height / img.Height);
				float scaledWidth = img.Width * scale * printScaleWidth;
				float scaledHeight = img.Height * scale * printScaleHeight;

				// 이미지를 페이지 크기에 맞춰 출력
				e.Graphics.DrawImage(img, 0, 0, scaledWidth, scaledHeight);
			}
			else
			{
				// 이미지가 페이지 크기보다 작을 경우, 그대로 중앙에 배치
				float offsetX = (pageBounds.Width - img.Width) / 2;
				float offsetY = (pageBounds.Height - img.Height) / 2;

				e.Graphics.DrawImage(img, offsetX, offsetY, img.Width, img.Height);
			}

			// 한 페이지로 완료
			e.HasMorePages = false;
		}
		catch (Exception ex)
		{
			Debug.LogError("Error loading or drawing image: " + ex.Message);
		}
	}
	else
	{
		Debug.LogError("File not found: " + printFilePath);
	}
}
```
