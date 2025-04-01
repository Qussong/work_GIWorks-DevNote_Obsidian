# 아이디어

---

# 학습 내용

### <span style="background:lightgray">파일 입출력</span>

#### 경로
- `Application.persistentDataPath`
	해당 경로는 Unity 프로젝트 외부의 쓰기 가능한 경로를 제공한다.
	해당 경로에 저장된 파일은 Unity가 관리하지 않기에 `.meta` 파일이 생성되지 않는다.
### <span style="background:lightgray">통신</span>

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
🔹**비동기 작업**
	효율성을 극대화할 수 있는 프로그래밍 방식으로 주요 장점은 아래와 같다.
	- 효율적인 리소스 활용
	- 응답성 향상
	- 확장성 향상
	- 데드락 예방
	비동기 작업이 적합한 상황으로는...
	- 대규모 네트워크 요청 처리
	- 파입 입출력 작업
	- 데이터베이스와의 비동기 통신
	- 실시간 사용자 경험 제공 (게임, 애니메이션, 등...)
#### 동기식 데이터 읽기 (Stream.Read)
```csharp
public abstract int Read(byte[] buffer, int offset, int count);
// buffer : 읽어온 데이터를 저장할 공간
// offset : 데이터가 저장이 시작될 위치
// count : 읽으려는 데이터의 최대 크기 
// return : 실제로 읽은 바이트 수 (읽은 데이터의 크기)
```
Read() 는 작업이 완료될 때까지 호출을 블로킹한다. 즉, 데이터 읽기가 완료되기 전까지 다른 작업을 진행하지 않는다.
스트림의 끝에 도달하면 읽은 바이트 수가 0으로 반환되며, 이를 통해 스트림의 종료 상태를 감지할 수 있다.
스트림이 닫혀있거나 읽기 작업이 지원되지 않을 경우, 예외가 발생한다. (IOException, ObjectDisposedException)
#### 비동기 데이터 읽기 (Stream.ReadAsync)
Unity 에서 비동기적으로 소켓 통신 데이터를 수신하려면 C#의 비동기 프로그래밍 방식을 활용하여 TcpClient 와 NetworkStream을 조합하는 방법이 가장 적합하다.
`aync` 와 `await` 키워드를 사용하여 데이터를 처리할 수 있다.
🔹**ReadAsync()**
	해당 함수는 기존 연결에 관계없이 적용할 수 있으며, **백그라운드에서 데이터를 읽어와 다른 작업과 병렬로 처리**할 수 있다.
🔹백그라운드 작업
	메인 스레드(main thread)가 아닌 다른 스레드에서 실행되는 작업을 의미한다.
	- 메인 스레드와 분리 : 메인 스레드는 주로 사용자 인터페이스, 입력처리, 게임 로직 등 사용자와 직접 관련된 작업을 담당하는 반면 백그라운드 작업은 파일 읽기, 데이터 처리, 게임 로직 등 시간이 오래 걸릴 수 있는 작업을 처리하는 등 메인 스레드가 멈추거나 느려지
	- 비동기 처리 :
	- 리소스 효율성 :


### <span style="background:lightgray">JsonUtility</span>

#### 직렬화 해제(Deserialization)
🔹JsonUtility.FromJson<T\>()
	해당 함수는 JSON 문자열을 직렬화 해제하여 지정된 타입<T\>의 객체를 생성하고 반환하는 Unity 메서드다.
	즉, 생성될 객체를 자동으로 생성하고 값을 채워 초기화한 상태로 반환한다. (new 키워드 사용안해도 됨)
```csharp
[Serializable]
public class EmotionData
{
    public int faceCount;
    public FaceData[] faces;
}

EmotionData receiveData = JsonUtility.FromJson<EmotionData>(jsonData);
```

### <span style="background:lightgray">Python</span>

#### 딕셔너리(Dictionary)
Python에서 데이터를 키-값(key-value) 쌍으로 저장하는 데이터 구조
🔹**구조**
	딕셔너리는 Python에서 `{key:value}` 형식으로 데이터를 저장한다.
	각 키는 고유하며, 중복될 수 없다.
```python
# 빈 딕셔너리
my_dict = {}
# 키와 값을 가지는 딕셔너리
person = {"name": "Alice", "age": 25, "city": "Seoul"}
```
🔹**주요 특징**
	- 키는 고유한 값이다.
	- 값은 중볼 가능하다.
	- Python 3.7 이후로 딕셔너리는 입력된 순서를 유지한다.
🔹**값 추가 및 수정 작업**
```python
person = {"name": "Alice", "age":25}

# 값 접근하기
print(person["name"]) # Alice
print(person["age"]) # 25

# 값 추가 및 수정하기
person["age"] = 26
person["city"] = "Seoul"
print(person) # {'name': 'Alice', 'age': 25, 'city': 'Seoul'}

# 값 삭제하기
del person["city"]
print(person) # {'name': 'Alice', 'age': 25}

# 키와 값 모두 확인하기
print(person.keys()) # dict_keys(['name', 'age'])
print(person.values()) # dict_values(['Alice', 25])
print(person.items()) # dict_items([('name', 'Alice'), ('age', 25)])
```

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


### <span style="background:lightgray">이미지 분석</span>

#### source code (python)
```python
    # 이미지
    image_path = os.path.join(path, "test.jpg") # 파일 경로 생성
    if os.path.exists(image_path):              # 파일 존재 여부 확인
        print("Exist image file.")
        image = cv2.imread(image_path)          # 이미지 파일 읽기

        # 이미지 로드 실패
        if image is None:
            print("Failed to load image")
            continue

        # 이미지 로드 성공
        print("Complete read image.")

        # 이미지 리사이징
        original_height, original_width = image.shape[:2]
        scale = 0.3
        new_width = int(original_width * scale)
        new_height = int(original_height * scale)
        resized_image = cv2.resize(image, (new_width, new_height), interpolation=cv2.INTER_AREA)

        # 이미지 분석을 위해 흑백 사진 변환
        gray_image = cv2.cvtColor(resized_image, cv2.COLOR_BGR2GRAY)
        faces = face_cascade.detectMultiScale(gray_image, scaleFactor=1.1, minNeighbors=5, minSize=(30, 30))
        face_data = []  # 얼굴의 감정 데이터를 담을 리스트

        # 얼굴 탐지 및 박스 그리기
        for (x, y, w, h) in faces:
            # 탐지된 얼굴 영역을 사각형으로 시각화
            cv2.rectangle(resized_image, (x, y), (x+w, y+h), (0, 255, 0), 2)    # 좌상단, 우하단, 사각형의 색상, 선의 두께
            # 얼굴 ROI(Region of Interest) 추출 및 전처리
            face_roi = gray_image[y:y+h, x:x+w] # 흑백 사진으로부터 얼굴의 영역을 잘라낸다.
            face_roi = cv2.resize(face_roi, (64, 64))   # 모델의 입력 크기에 맞게 ROI 를 64x64 크기로 리사이즈
            face_roi = np.expand_dims(face_roi, axis=-1)# 차원을 확장하여 height,width,channels 형식으로 만든다 (흑백 이미지이기에 채널이 1개)
            face_roi = np.expand_dims(face_roi, axis=0) # 다시 차원을 확장하여 모델 입력 형식인 (batch_size,height,width,channels)로 만든다
            face_roi = face_roi / 255.0 # 픽셀 값을 [0,1]범위로 정규화하여 모델의 입력 값으로 적합하게 변환
            # 모델 예측 및 감정 분류
            output = model.predict(face_roi)[0] # 전처리된 얼굴 이미지를 감정 분석 모델에 입력
            expression_index = np.argmax(output)# 가장 높은 확률을 가진 감정의 인덱스 반환
            expression_label = expression_labels[expression_index]  # 인덱스에 해당하는 감정 레이블 반환
            # 결과를 이미지에 출력
            cv2.putText(resized_image, expression_label, (x, y-10), cv2.FONT_HERSHEY_SIMPLEX, 0.9, (0, 255, 0), 2)  # 감정 레이블을 탐지된 얼굴 상단에 텍스트로 표시

```

#### output
![500](Pasted%20image%2020250331090706.png)

### <span style="background:lightgray">데이터 전송 및 가공</span>

현재 동기식으로 1FPS 의 속도로 웹캠에 찍히는 장면을 파일 입출력으로 외부에 저장하고, 서버 역할을 하는 Python 에서 해당 파일을 읽어오고 있다.
때문에 Python 에서 분석한 해당 내용을 Unity 에서 받되, 블로킹 되어 메인 로직이 멈추면 안된다.
이를 해결할 수 있는 방법은 아래와 같다.
1. ✅ReadAsync 방식
2. thread 방식
#### source code (unity)
Python이 분석한 데이터를 
```csharp
private CancellationTokenSource cancelToken = null;
private EmotionData receiveData = null;

void Update()
{
	// 표정 분석 데이터 수신
	_ = ReceiveDataAsync();
}

void OnDisable()
{
	// 비동기 취소 요청
	cancelToken.Cancel();
}

private async Task ReceiveDataAsync()
{
	byte[] buffer = new byte[1024];
	try
	{
		while (!cancelToken.Token.IsCancellationRequested)    // 지속적인 수신 루프
		{
			// 데이터 수신 시도
			int bytesRead = await stream.ReadAsync(buffer, 0, buffer.Length, cancelToken.Token);
			if (bytesRead > 0)
			{
				// 수신된 데이터를 utf-8 문자열로 변환
				string jsonData = Encoding.UTF8.GetString(buffer, 0, bytesRead);
				// 수신된 데이터 기반으로 Emotion 객체 초기화
				receiveData = JsonUtility.FromJson<EmotionData>(jsonData);
			}
			else
			{
				// 데이터를 받지 못했음 연결이 닫히지 않았다면 계속 대기
				Debug.Log("현재 데이터가 없음, 대기 중 ...");
				await Task.Delay(100, cancelToken.Token);
			}
		}
	}
	catch (Exception ex)
	{
		string endMsg = "A task was canceled.";
		if(endMsg.Equals(ex.Message))
		{
			Debug.Log("Connection to the server has been terminated successfully.");
		}
		else
		{
			Debug.LogError($"데이터 수신 중 요류 발생: {ex.Message}");
		}
	}
}
```


