# 아이디어

---

# 학습 내용

### <span style="background:lightgray">파일 입출력</span>

#### 경로
- `Application.persistentDataPath`
	해당 경로는 Unity 프로젝트 외부의 쓰기 가능한 경로를 제공한다.
	해당 경로에 저장된 파일은 Unity가 관리하지 않기에 `.meta` 파일이 생성되지 않는다.

#### 소켓통신
🔹**TcpClient.GetStream()**
	유니티에서 TCP 통신을 구현할 때 사용하는 메서드
	클라이언트와 서버 간의 데이터 송수신을 위한 **Stream 객체를 반환**한다.
	해당 스트림 객체는 데이터를 읽고 쓰는 데 사용된다. ()
🔹**System.IO.Stream**
	추상 클래스로 다양한 스트림을 다룰 수 있도록 공통 인터페이스를 제공한다.
	하위 클래스가 특정 유형의 스트림을 구현한다. (FileStream, MemoryStream, NetworkStream etc...)
🔹**NetworkStream**
	TCP소켓과 직접 연결되어 데이터를 송수신하기에 소켓 통신 구현시 사용하기 적합하다.
	- TCP 소켓 기반
	- 실시간 데이터 전송
	- 비동기 작업 지원
	- 간단한 작업 흐름
🔹비동기 작업
	효율성을 극대화할 수 있는 프로그래밍 방식으로 주요 장점은 아래와 같다.
	- 



---

# 코드 구현
### <span style="background:lightgray">소켓통신 (unity-python)</span>

#### source code (python)
```python
import socket

# Python-Unity Network
host = '127.0.0.1'  # 로컬호스트, IP주소
port = 5000         # 유니티와 통신할 포트
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.bind((host, port))
server_socket.listen(1) # 클라이언트 연결 수 (1명만 연결)

print("Waiting for Unity to connect ...")
connection, address = server_socket.accept()    # Unity가 연결되기를 기다림
print(f"Connected to Unity at {address}")

connection.close()
```

#### source code (unity)
```csharp
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
```

#### output
![400](connectionTestOutput.png)
출력값에 나온 54657은 클라(유니티)가 소켓 통신을 위해 자동으로 할당 받은 포트다.
소켓  통신에는 IP 주소와 함께 포트 번호가 필요하다. 현재 서버(Python)는 5000번 포트를 열고 클라를 기다리고 있고, 클라 역시 통신을 시작하기 위해 자신만의 임시 포트(`클라이언트 포트 번호`)를 할당받아야한다.

이 번호는 OS가 자동으로 관리하며, 통신 중에 유일하게 클라를 식별하는 데 사용된다. 동적으로 변하는 값이기에, 연결할 때마다 다른 포트 번호가 할당될 수 있다.

프로그래밍 관점에서는 클라이언트는 서버의 IP와 포트를 사용하고, 서버는 연결된 클라이언트 소켓을 통해 통신을 처리하므로 클라이언트 포트를 직접 지정하거나 신경 쓰지 않아도 된다.

### <span style="background:lightgray">경로 전달 & 이미지 저장</span>

#### source code (unity)
```csharp
using System;
using System.IO;
using System.Net.Sockets;
using UnityEngine;
using UnityEngine.UI;

public class Webcam : MonoBehaviour
{
    TcpClient client;
    Stream stream;
    string path;

    private WebCamTexture webcamTexture;
    private float shutterTimer = 0;

    void Start()
    {
        // Cam Check
        foreach(var camera in WebCamTexture.devices)
        {
            Debug.Log("Webcam found : " + camera.name);
        }

        // Connection
        try
        {
            // Python 소켓 서버와 연결
            client = new TcpClient("127.0.0.1", 5000);
            stream = client.GetStream();
            Debug.Log("Connected to Python server.");

            // Path 전송
            path = Application.persistentDataPath;
            SendData(path);
            //Debug.Log($"Sent path to Python : {path}");
        }
        catch (Exception ex)
        {
            Debug.LogError("Connection Error : " + ex.Message);
        }

        // Generate Webcam Texture
        webcamTexture = new WebCamTexture();
        Image img = GetComponent<Image>();
        if(null != img)
        {
            img.material = new Material(Shader.Find("UI/Default"));
            img.material.mainTexture = webcamTexture;

            // Start Webcam
            webcamTexture.Play();

            // 가로,세로 비율 계산
            float aspectRatio = (float)webcamTexture.width / webcamTexture.height;
            float newWidth = aspectRatio * img.rectTransform.rect.height;
            img.rectTransform.sizeDelta = new Vector2(newWidth, img.rectTransform.rect.height);
        }
        else
        {
            Debug.LogError("Image 컴포넌트가 없습니다.");
        }

    }

    void Update()
    {
        if(Input.GetKeyDown(KeyCode.Space))
        {
            SaveWebcamFrame();
        }
    }

    private void SaveWebcamFrame()
    {
        // 텍스처 데이터 읽기
        Texture2D snapshot = new Texture2D(webcamTexture.width, webcamTexture.height, TextureFormat.RGB24, false);
        snapshot.SetPixels(webcamTexture.GetPixels());
        snapshot.Apply();

        // PNG 형식으로 저장
        byte[] bytes = snapshot.EncodeToPNG();
        string fileName = "test.jpg";
        string filePath = Path.Combine(path, fileName);
        File.WriteAllBytes(filePath, bytes);

        SendData("PHOTO");
    }

    void OnDisable()
    {
        // 통신 종료 신호 서버로 명시적으로 전달
        SendData("EXIT");

        // Stop Webcam
        webcamTexture.Stop();
    }

    void SendData(string data)
    {
        byte[] byteData = System.Text.Encoding.UTF8.GetBytes(data);
        stream.Write(byteData, 0, byteData.Length);

        // Signal
        if("PHOTO" == data)
            Debug.Log("Save Phto");
        if("EXIT" == data)
            Debug.Log("Sent shutdown signal to Server");
    }

}

```

#### source code (python)
```python

import socket
import os
import cv2

# 함수1
def start_server_socket(port):
    host = '127.0.0.1'  # 로컬호스트, IP주소
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server_socket.bind((host, port))
    server_socket.listen(1) # 클라이언트 연결 수 (1명만 연결)

    print("Waiting for Unity to connect ...")
    connection, address = server_socket.accept()    # Unity가 연결되기를 기다림
    print(f"Connected to Unity at {address}")
    return connection, server_socket

# 함수2
def recv_signal(connection):
    data = connection.recv(1024)
    if data:
        message = data.decode('utf-8')
        if "EXIT" == message.strip():
            print("Shutdown signal received. Closing server...")
            return True
        if "PHOTO" == message.strip():
            print("Client receive image.")
            return False
    return False

# 메인 로직
connection, server_socket = start_server_socket(5000)  # 소켓 생성 및 연결 대기
data = connection.recv(1024)    # 1024 바이트 읽기
path = data.decode('utf-8')     # UTF-8 로 디코딩

while True:
    # 클라이언트 신호 (PHTO, EXIT)
    if recv_signal(connection):
        break

    # 이미지 출력
    image_file = os.path.join(path, "test.jpg") # 파일 경로 생성
    if os.path.exists(image_file):              # 파일 존재 여부 확인
        print("Exist image file.")
        image = cv2.imread(image_file)          # 이미지 파일 읽기
        if image is not None:
            print("Complete read image.")
            cv2.imshow("Image", image) # 화면에 이미지 출력
            cv2.waitKey(1)

            try:
                os.remove(image_file)   # 읽어온 이미지 삭제
                print(f"Delete image.")
            except Exception as e:
                print(f"Failed to delete file: {e}")

        else:
            print("Failed to load image")
                
# 연결 닫기
cv2.destroyAllWindows()
connection.close()
server_socket.close()
```
#### output
🔹**경로 전달**
	![600](persistentDataPath.png)
🔹이미지 Save/Read/Delete
	![600](imageSRD.png)


### <span style="background:lightgray">이미지 분석 & 데이터 전송</span>

#### source code (python)
```python

```


#### source code (unity)
```csharp

```
🔹
#### output
![500](Pasted%20image%2020250331090706.png)
