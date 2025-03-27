
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
    # 데이터를 JSON 형식으로 직렬화화
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