
```csharp
using System;
using System.IO;
using System.Net.Sockets;
using System.Text;
using UnityEngine;

[Serializable]
public class EmotionData
{
    public int faceCount;
    public FaceData[] faces;
}

[Serializable]
public class FaceData
{
    public int expressionIdx;
    public string expression;
    public Position position;
}

[Serializable]
public class Position
{
    public int x;
    public int y;
    public int width;
    public int height;
}

public class EmotionReceiver : MonoBehaviour
{
    TcpClient client;
    Stream stream;

    void Start()
    {
        try
        {
            // Python 소켓 서버와 연결
            client = new TcpClient("127.0.0.1", 5000);
            stream = client.GetStream();
            Debug.Log("Connected to Python server.");
        }
        catch (Exception ex)
        {
            Debug.LogError("Connection Error : " + ex.Message);
        }
    }

    void Update()
    {
        try
        {
            if (null != stream && stream.CanRead)
            {
                byte[] data = new byte[1024];   // temp buffer
                int bytesRead = stream.Read(data, 0, data.Length);  // read data

                if (bytesRead > 0)   // 읽혀진 데이터가 있으면 처리
                {
                    string jsonData = Encoding.UTF8.GetString(data, 0, bytesRead);

                    // JSON 데이터 파싱
                    EmotionData receiveData = JsonUtility.FromJson<EmotionData>(jsonData);

                    // 얼굴 개수와 감정 출력
                    Debug.LogWarning($"Face Count: {receiveData.faceCount}");
                    foreach (var face in receiveData.faces)
                    {
                        Debug.Log($"Expression: {face.expression} ({face.expressionIdx})" + ", Position: " +
                            $"(x : {face.position.x}, y : {face.position.y}, width : {face.position.width}, height : {face.position.height})");
                    }

                }
            }
        }
        catch (Exception ex)
        {
            Debug.LogError("Error reading data: " + ex.Message);
        }

        void OnApplicationQuit()
        {
            Debug.Log("Quit Connection.");
            stream?.Close();
            client?.Close();
        }
    }
}

```