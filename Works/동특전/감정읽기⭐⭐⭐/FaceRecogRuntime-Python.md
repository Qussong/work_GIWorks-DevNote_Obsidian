
```python
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
```