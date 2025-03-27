
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

#### 패킷 통신과의 연관성

이 코드는 애플리케이션 레벨에서 JSON 데이터를 작성하고 전송하는 역할을 한다.
