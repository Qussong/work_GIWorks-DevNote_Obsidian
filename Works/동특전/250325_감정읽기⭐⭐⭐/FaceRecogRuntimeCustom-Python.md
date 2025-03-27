
```python
import cv2
import dlib
import numpy as np
from tensorflow.keras.models import load_model

import time

face_cascade = cv2.CascadeClassifier('./models/haarcascade_frontalface_default.xml')
predictor = dlib.shape_predictor('./models/shape_predictor_68_face_landmarks.dat')
expression_labels = ['Angry', 'Disgust', 'Fear', 'Happy', 'Sad', 'Surprise', 'Neutral']
model = load_model('./models/emotion_model.hdf5', compile=False)
model.compile(optimizer='adam', loss='categorical_crossentropy')

video_capture = cv2.VideoCapture(0)
while True:

    # 프레임 처리 시작 시간 기록
    start_time = time.time()

    ret, frame = video_capture.read()
    if not ret:
        break

    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    faces = face_cascade.detectMultiScale(gray, scaleFactor=1.1, minNeighbors=5, minSize=(30, 30))
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
    
    cv2.imshow('Expression Recognition', frame)

    # 프레엄 처리 시간 계산 및 갱신 주기 설정
    # 초당 한 장 처리(5fps)를 유지하기 위한 대기 시간 설정
    # 대기 시간을 나누어 키 입력을 처리
    while time.time() - start_time < 0.2:
        if cv2.waitKey(10) & 0xFF == ord('q'):  # 10ms마다 키 입력 감지
            break
    else:
        continue  # while True로 돌아감
    break  # 'q'가 눌렸을 때 루프 종료


if video_capture.Opened():
    video_capture.release()
cv2.destroyAllWindows()
```