# <span style="background:lightgray">Content</span>

### 기획 및 내용
| Zone    | Corner | Item | No    | 연출개요                                 | 예상난이도 |
| ------- | ------ | ---- | ----- | ------------------------------------ | ----- |
| Zone 02 | 뇌와감정   | 감정읽기 | 3-1-3 | 카메라를 통해 자신의 표정을 인식하여 <br>감정을 분석하는 체험 | ⭐⭐    |
![](감정읽기-세부연출계획.png)

### 감정별 Rect 색상
- Angry : 빨강
- disgust : 보라
- Fear : 초록
- Happy : 노랑
- Sad : 진한 블루
- Surprise : 하늘
- Neutral : 주황

---
# <span style="background:lightgray">Install & Repair</span>
### N회차
Date : 
Memo :
	- !!!
	- !!!

---
# <span style="background:lightgray">Dev Note</span>

### 기술스택

**🔹요구 기능 및 기술**
- Python
- 안면인식(OpenCV)
- 감정 분석(AI Model)
- Unity-Python 통신(Socket 통신)

**🔹라이브러리**
코드에서 사용하는 주요 라이브러리
- `OpenCV (cv2)`
- `Dlib`
- `NumPy`
- `TensorFlow`
![500](라이브러리.png)

**🔹모델**
- `haarcascade_frontalface_default` : 얼굴 인식을 위한 OpenCV 모델
- `shape_predicator_68_face_landmarks.dat` : 얼굴의 랜드마크를 예측하기 위한 dlib 모델
- `emotion_model.hdf5` : 감정을 예측하기 위한 미리 학습된 딥러닝 모델

### Version 1 : Python 에서 Webcam 접근
파이썬으로 웹캠에 접근해 얼굴에 대한 정보를 얻고, 분석한 데이터를 소켓통신으로 Unity 에 전송한다.
[Dev Note Ver1](Dev%20Note%20Ver1.md)

### Version 2 : Unity 에서 Webcam 접근
Unity 에서 웹캠에 접근해 촬영되는 이미지를 얻고, 이를 외부 경로에 저장한다.
저장된 이미지를 Python이 읽어와 사람 얼굴을 인식하고 표정을 분석한다.
이후, 분석한 내용을 소켓통신으로 Unity에 전달한다.
[Dev Note Ver2](Dev%20Note%20Ver2.md)

---
