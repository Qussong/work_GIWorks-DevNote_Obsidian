
# 아이디어

파이썬을 통해 웹캠에 접근하여 카메라를 통해 얼굴을 인식하고 표정을 분석함
이렇게 분석된 데이터를 Unity 에 소켓통신으로 전달
유니티는 전달받은 데이터를 가공하여 컨텐츠에 활용

# 구현

### <span style="background:lightgray">표정 분석</span>
#### source code (python)
```python
import cv2
import numpy as np
import dlib
from tensorflow.keras.models import load_model

# 이미지 분석, 얼굴 감지
face_cascade = cv2.CascadeClassifier('./models/haarcascade_frontalface_default.xml')
# 표정 인식을 위한 눈,코,입 등의 위치 반환
predictor = dlib.shape_predictor('./models/shape_predictor_68_face_landmarks.dat')

# 표정 가중치 모델
model = load_model('./models/emotion_model.hdf5', compile=False)
model.compile(optimizer='adam', loss='categorical_crossentropy')
expression_labels = ['Angry', 'Disgust', 'Fear', 'Happy', 'Sad', 'Surprise', 'Neutral']

video_capture = cv2.VideoCapture(0)
# cv2.VideoCapture(0) : 기본 웹캠을 연다.
#                       외장 카메라를 사용할 경우 1,2 등으로 번호를 변경하면 된다.
#                       동영상 파일을 사용할 수도 있다. ex) cv2.VideoCapture("video.mp4")

while True:
    ret, frame = video_capture.read()
    # video_capture : cv2.VideoCapture() 객체로, 웹캠 혹은 동영상 파일에서 프레임을 가져오는 역할
    # video_capture.read() : 한 프레임(현재 프레임임)을 읽어온다. 
    #                       ret == False 면 카메라가 없거나 접근이 불가능한 상태를 의미한다.
    # ret : 프레임을 제대로 읽어오면 true, 실패하면 false 를 반환
    # frame : 읽어온 이미지 데이터를 NumPy 배열 형식으로 반환

    if not ret:
        break

    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    # 컬러 이미지(frame)를 그레이스케일 이미지로 변환
    # frame 은 일반적으로 BGR(Blue, Green, Red)의 컬러 이미지를 나타낸다.
    # 해당 함수는 밝기 정보만 포함된 그레이스케일 이미지를 반환한다.
    # 그레이 스케일 변환은 컴퓨터 비전 작업에 유용하다.
    #   1. 계산 비용이 감소한다 (3채널 대신 1채널만 사용)
    #   2. 알고리즘에서 불필요한 컬러 정보를 제거한다.

    faces = face_cascade.detectMultiScale(gray, scaleFactor=1.1, minNeighbors=5, minSize=(30, 30))
    # face_cascade : OpenCV의 Haar Cascade 클래스 객체
    #               일반적으로 미리 학습된 얼굴 감지 모델 (ex: haarcascade_frontalface_default.xml)
    #               을 로드하여 사용한다.
    # 매개변수 :
    #   1. gray : 그레이스케일 이미지로, 얼굴 감지는 컬러 이미지보다 그레이스케일 이미지에서 더 효율적
    #   2. scaleFactor : 이미지에서 객체 크기를 조정하는 비율
    #                   1.1 은 조금씩 크기를 증가시키며 탐색한다는 의미이다.
    #                   값이 작을수록 더 많은 크기를 탐색하지만, 계산 비용이 증가한다.
    #   3. minNeighbors : 객체가 감지되었다고 판단하기 위해 필요한 최소 이웃의 수
    #                   5 는 감지된 영역 주변의 사각형이 최소 5개 이상 중첩되어야 얼굴로 간주한다.
    #                   값을 증가시키면 더 정확한 결과를 얻을 수 있지만, 얼굴 감지가 어려워질 수 있다.
    #   4. minSize : 감지할 객체의 최소 크기
    #               (30,30) 픽셀 이상의 크기만 얼굴로 감지한다.
    # 반환값 :
    #   - faces : 얼굴로 감지된 모든 영역의 좌표를 반환한다.
    #           반환되는 값은 (x,y,w,h) 형태의 리스트 (정수형형)
    #           x, y : 감지된 얼굴의 왼쪽 위 좌표
    #           w, h : 감지된 얼굴의 너비와 높이

    for (x,y,w,h) in faces:
        # 얼굴 크기에 알맞도록 사각형 그리기
        cv2.rectangle(frame,(x,y), (x+w, y+h), (0,255,0), 2)

        # 얼굴 크기 반환
        face_roi = gray[y:y+h, x:x+w]
        # 얼굴 영역의 부분 이미지를 회색조(grayscale)로 추출한다.
        # ROI (Region of Interest)는 추출된 얼굴 영역에만 집중하여 분석 속도를 높이고 
        # 메모리를 절약한다.

        # 표정을 인식하기 위해 표정 dataset과 똑같은 사이즈 변환
        # dataset 이미지와 입력된 얼굴의 크기가 다르면 error 발생
        face_roi = cv2.resize(face_roi, (64, 64))
        # 표정 분석 모델과 동일한 크기로 얼굴 이미지를 리사이즈한다.
        # 입력 크기를 맞추지 않으면 모델이 데이터를 처리할 수 없기에 필수작업이다.

        face_roi = np.expand_dims(face_roi, axis=-1)
        face_roi = np.expand_dims(face_roi, axis=0)
        # 데이터를 모델 입력 형식에 맞게 차원을 추가한다.
        # (64,64) 크기의 2D 이미지를 4D 텐서 형태 (1,64,64,1)로 변경한다.

        face_roi = face_roi / 255.0
        # 픽셀 값을 [0,1] 범위로 정규화하여 모델의 학습 데이터와 일치하도록 만든다.
        # 이는 모델이 숫자 스케일의 영향을 덜 받도록 도와준다.

        # 모델을 통해 표정 분석
        output = model.predict(face_roi)[0]
        # 전처리된 얼굴 이미지를 사용해 학습된 모델로 표정을 예측한다.
        # 출력값 output은 각 표정 클래스에 대한 확률(softmax 결과) 배열이다.

        # 해당 표정의 값 반환
        expression_index = np.argmax(output)
        # 가장 높은 확률을 가진 클래스의 인덱스를 반환한다.

        # 표정에 따른 label 값 저장
        expression_label = expression_labels[expression_index]
        # 인덱스를 기반으로 표정 클래스 이름(label)을 가져온다.

        # 표정 값 출력
        cv2.putText(frame, expression_label, (x, y-10), cv2.FONT_HERSHEY_SIMPLEX, 0.9, (0,255,0), 2)
        # 인식된 표정을 이미지에 텍스트로 표시한다.
        # FONT_HERSHEY_SIMPLEX는 텍스트 폰트 스타일, (x, y-10)은 텍스트의 위치를 지정한다.

    cv2.imshow('Webcam', frame)
    # cv2.imshow(window_name, image)
    # window_name : 생성할 창의 이름 (문자열)
    # image : 화면에 표시할 이미지 (NumPy 배열, 즉 OpenCV 이미지)

    if cv2.waitKey(1) & 0xFF == ord('q'):
        break
    # cv2.waitKey(1) : 1ms(0.001 초) 동안 키 입력을 기다림
    #                   사용자가 입력한 키의 ASCII 코드(정수값)를 반환한다.
    #                   입력이 없으면 -1 을 반환한다.
    # cv2.waitKey(1) & 0xFF : 입력된 키 값을 8비트(0xFF)로 변환
    # ord('q') : ord() 함수는 문자를 해당하는 ASCII 또는 유니코드 정수 값으로 변환한다.
    #           반대 역할을 하는 함수로 char(num) 이 있다.

if video_capture.isOpened():
    print("video capture close")
    video_capture.release()
    
cv2.destroyAllWindows()

```

### <span style="background:lightgray">Socket 통신 추가</span>
#### source code (python)

- TCP 소켓을 통해 Unity에 전송 (서버 역할)
- 얼굴 인식 및 감정 데이터를 JSON 형식으로 변환
- 성능을 고려하고 5Frame 으로 화면이 갱신되도록 수정

```python
import cv2
import dlib
import numpy as np
from tensorflow.keras.models import load_model
import time

import socket
import json

# Model Setting
face_cascade = cv2.CascadeClassifier('./models/haarcascade_frontalface_default.xml')
predictor = dlib.shape_predictor('./models/shape_predictor_68_face_landmarks.dat')
expression_labels = ['Angry', 'Disgust', 'Fear', 'Happy', 'Sad', 'Surprise', 'Neutral']
model = load_model('./models/emotion_model.hdf5', compile=False)
model.compile(optimizer='adam', loss='categorical_crossentropy')

# Python-Unity Network
host = '127.0.0.1'  # 로컬호스트, IP주소
port = 5000         # 유니티와 통신할 포트
server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_socket.bind((host, port))
server_socket.listen(1) # 클라이언트 연결 수 (1명만 연결)
print("Waiting for Unity to connect ...")
connection, address = server_socket.accept()    # Unity가 연결되기를 기다림
print(f"Connected to Unity at {address}")

# Main Logic
video_capture = cv2.VideoCapture(0)
while True:

    start_time = time.time()

    ret, frame = video_capture.read()
    if not ret:
        break

    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    faces = face_cascade.detectMultiScale(gray, scaleFactor=1.1, minNeighbors=5, minSize=(30, 30))

    face_data = []  # 얼굴의 감정 데이터를 담을 리스트트

    for (x, y, w, h) in faces:
        cv2.rectangle(frame, (x, y), (x+w, y+h), (0, 255, 0), 2)
        face_roi = gray[y:y+h, x:x+w]
        face_roi = cv2.resize(face_roi, (64, 64))
        face_roi = np.expand_dims(face_roi, axis=-1)
        face_roi = np.expand_dims(face_roi, axis=0)
        face_roi = face_roi / 255.0
        output = model.predict(face_roi)[0]
        expression_index = np.argmax(output)
        expression_label = expression_labels[expression_index]
        cv2.putText(frame, expression_label, (x, y-10), cv2.FONT_HERSHEY_SIMPLEX, 0.9, (0, 255, 0), 2)

        # 감정 데이터 추가
        face_data.append({
            "expressionIdx": int(expression_index),
            "expression": expression_label,
            "position": {"x": int(x), "y": int(y), "width": int(w), "height": int(h)}
        })

    # 얼굴 개수와 데이터를 JSON 형식으로 변환
    # print("Data to send Cnt :", len(face_data))
    data_to_send = {
        "faceCount": len(face_data),
        "faces": face_data
    }
    # 데이터를 JSON 형식으로 직렬화
    json_data = json.dumps(data_to_send)
    # Unity에 데이터 전송
    connection.sendall(json_data.encode('utf-8'))

    # 화면 송출
    cv2.imshow('Expression Recognition', frame)

    # 프레임 관리 (fps : 5)
    while time.time() - start_time < 0.2:
        if cv2.waitKey(10) & 0xFF == ord('q'):
            break
    else:
        continue
    break

connection.close()
if video_capture.Opened():
    video_capture.release()
cv2.destroyAllWindows()
```

#### source code (unity,csharp)

- C#에서 TCP 클라이언트를 사용하여 데이터 수신 (클라이언트 역할)
- JSON 데이터를 Unity의 객체로 파싱해 활용

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


### <span style="background:lightgray">JSON 과 UTF-8</span>

#### UTF-8 의 장점
- 글로벌 호환성 : 
	UTF-8은 세계 **대부분의 문자를 지원**한다. JSON 데이터를 UTF-8로 인코딩하여 다국어 데이터를 처리하거나 송수신하는 데 문제가 없다.
- 효율성 : 
	UTF-8 은 ASCII 에 대해 1바이트만 사용하기에, **데이터 크기를 최소화**할 수 있다. 다른 인코딩 방식은 기본적으로 더 많은 바이트를 사용한다.
- 표준 인코딩 : 
	UTF-8은 **웹과 네트워크 프로토콜에서 가장 널리 사용되는 인코딩 방식**이다. 수신 측에서도 UTF-8 데이터를 손쉽게 디코딩할 수 있다.

#### JSON 의 장점
- 가벼움 :
	JSON은 데이터 구조를 표현하기 위한 **간결한 형식으로, 전송 크기가 작아 네트워크 사용량을 줄일 수 있다.**
- 가독성 :
	사람이 **읽기 쉽고, 디버깅 시에도 편리**하다.
- 표준화된 포맷 :
	JSON은 많은 프로그래밍 언어와 플랫폼에서 기본적으로 지원하기에 데이터를 쉽게 직렬화 및 역직렬화할 수 있다.

#### JSON이 간결하지만 UTF-8이 필요한 이유
JSON은 간결한 데이터 구조를 제공하지만, 네트워크 전송의 효율성과 국제화 및 호환성을 위해 UTF-8 로 인코딩하여 전송하는 것이 중요하다.
이렇게 하면 데이터가 손상되거나 깨질 위험 없이 안정적으로 송수신된다.

> **"JSON 데이터를 네트워크로 전송하려면 데이터를 바이트(byte) 형태로 변환해야 한다. 이때 UTF-8로 인코딩하면, JSON 데이터는 추가적인 변환 과정 없이 그대로 네트워크에서 전송할 수 있다. UTF-8은 표준화된 방식으로, 수신 측에서도 데이터를 쉽게 디코딩하여 원래의 텍스트 또는 객체로 복원하고 가공할 수 있다."**

1. JSON은 텍스트 기반
	- JSON은 문자를 기반으로 데이터를 표현한다. 때문에 네트워크를 통해 데이터를 전송할 때, 문자열 데이터를 바이트 기반으로 변환해야한다.
	- 텍스트 데이터를 **효율적으로 처리**하기 위해 JSON 데이터를 UTF-8로 인코딩하여 바이트 형태로 전송하는 것이 필수적이다.
2. 네트워크 표준을 따른 호환성
	- 대부분의 네트워크 및 인터넷 프로토콜(HTTP, WebSocket)은 데이터를 UTF-8로 인코딩할 것을 권장한다.
	- UTF-8은 ASCII와 하위 호환성을 제공하며, 다양한 플랫폼 간 동일하게 처리될 수 있어 전 세계적으로 표준 인코딩 방식으로 자리잡았다.
3. 다국어 및 특수문자 지원
	- JSON 자체는 유니코드를 기반으로 다국어와 특수문자를 지원한다. 하지만 네트워크를 통해 전송하려면 이를 적절히 UTF-8로 변환해야 데이터 손실 또는 깨짐 현상을 방지할 수 있다.
	- 한글이나 이모지 같은 복잡한 문자는 UTF-8 인코딩을 통해 안정적으로 전송된다.
4. 네트워크 전송 효율성
	- UTF-8은 가변 길이 인코딩 방식으로, ASCII 문자에는 1바이트만 사용해 데이터를 매우 효율적으로 전송한다.
	- JSON 데이터가 영어 위주라면 전송 크기를 최소화할 수 있어 네트워크 사용량을 더욱 줄일 수 있다.
5. UTF-8의 디코딩 규칙성과 일관성
	- 수신 측에서는 UTF-8 인코딩 규칙을 기반으로 데이터를 쉽게 디코딩할 수 있다. 이는 JSON과 UTF-8의 조합이 네트워크 환경에서 안정적이고 표준화된 방식이기 때문이다.

### 유니코드와 UTF-8

유니코드와 UTF-8은 서로 다른 개념이지만, 밀접한 관련이 있다.
유니코드는 문자 집합이고, UTF-8은 유니코드 문자를 저장하거나 전송하기 위한 인코딩 방식 중 하나이다.

#### 유니코드 (Unicode)
- 정의 : 전 세계의 문자를 표현하기 위한 글자 집합 또는 문자 코드 표준
- 역할 : 각 문자를 고유한 숫자로 매핑하여 언어와 플랫폼에 관계없이 동일하게 표현할 수 있도록 한다.
- 문자만 정의 : 단순히 어떤 문자가 어떤 숫자에 대응하는지만 정의한다. 이를 저장하거나 전송하려면 별도의 인코딩 방식을 사용해야한다.
- 표현 방식 : '가' = `U+AC00`

#### UTF-8 (Universal Coded Character Set Transformation Format-8 bit)
- 정의 : 한 문자를 표현하는 데 1~4바이트를 사용한다. 
  (가변길이 인코딩 , 영어 1바이트/한글 3바이트/이모지 4바이트)
- ASCII와 호환 : 영어 같은 기존 ASCII 문자는 그대로 유진된다.
- 효율성 : 텍스트의 내용에 따라 필요한 바이트만 사용하기에, 단순한 텍스트에 적합하다.
- 작동 원리 : 유니코드 코드 포인트를 읽고, 이를 바이트로 변환 -> 수신 측에서는 이 바이트들을 다시 유니코드 코드 포인트로 복원
- 표현 방식 : '가' = `1110xxxx 10xxxxxx 10xxxxxx`



