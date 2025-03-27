
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

### Overview

🔹**json.dumps(data_to_send)**
	데이터를 JSON 형식으로 변환하여 문자열로 만든다.
	이를 통해 데잉터를 구조화된 텍스트 형태로 직렬화한다.
	
🔹**connection.sendall(json_data.encode('utf-8'))**
	`socket.sendall()` 함수는 데이터를 바이트 형태로 전송한다.
	위의 코드에서는 JSON 데이터를 UTF-8로 인코딩하여 소켓을 통해 전송한다.
	전송된 데이터는 네트워크 상에서 패킷으로 처리되며, 네트워크 레이어에서 데이터를 전송하고 수신한다.

🔹**UTF-8 의 장점**
	- 글로벌 호환성 :
	  UTF-8은 세계 **대부분의 문자를 지원**한다. JSON 데이터를 UTF-8로 인코딩하여 다국어 데이터를 처리하거나 송수신하는 데 문제가 없다.
	- 효율성 : 
	  UTF-8 은 ASCII 에 대해 1바이트만 사용하기에, **데이터 크기를 최소화**할 수 있다. 다른 인코딩 방식은 기본적으로 더 많은 바이트를 사용한다.
	- 표준 인코딩 :
	  UTF-8은 **웹과 네트워크 프로토콜에서 가장 널리 사용되는 인코딩 방식**이다. 수신 측에서도 UTF-8 데이터를 손쉽게 디코딩할 수 있다.

🔹**JSON 의 장점**
	- 가벼움 :
	  JSON은 데이터 구조를 표현하기 위한 **간결한 형식으로, 전송 크기가 작아 네트워크 사용량을 줄일 수 있다.**
	- 가독성 : 사람이 **읽기 쉽고, 디버깅 시에도 편리**하다.
	- 표준화된 포맷 :
	  JSON은 많은 프로그래밍 언어와 플랫폼에서 기본적으로 지원하기에 데이터를 쉽게 직렬화 및 역직렬화할 수 있다.

🔹**JSON이 간결하지만 UTF-8이 필요한 이유**
	JSON은 간결한 데이터 구조를 제공하지만, 네트워크 전송의 효율성과 국제화 및 호환성을 위해 UTF-8 로 인코딩하여 전송하는 것이 중요하다.
	이렇게 하면 데이터가 손상되거나 깨질 위험 없이 안정적으로 송수신된다.
	1. JSON은 텍스트 기반
		- JSON은 문자를 기반으로 데이터를 표현한다. 때문에 네트워크를 통해 데이터를 전송할 때, 문자열 데이터를 바이트 기반으로 변환해야한다.
		- 텍스트 데이터를 **효율적으로 처리**하기 위해 JSON 데이터를 UTF-8로 인코딩하여 바이트 형태로 전송하는 것이 필수적이다.
		(효율적인 이유? : )
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