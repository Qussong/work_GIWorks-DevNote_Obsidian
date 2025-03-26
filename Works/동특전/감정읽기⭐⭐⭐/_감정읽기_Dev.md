# <span style="background:#f5f5dc">Content</span>

### 기획 및 내용
| Zone    | Corner | Item | No    | 연출개요                                 | 예상난이도 |
| ------- | ------ | ---- | ----- | ------------------------------------ | ----- |
| Zone 02 | 뇌와감정   | 감정읽기 | 3-1-3 | 카메라를 통해 자신의 표정을 인식하여 <br>감정을 분석하는 체험 | ⭐⭐    |
![](감정읽기-세부연출계획.png)

---
# <span style="background:#f5f5dc">Install & Repair</span>



---
# <span style="background:#f5f5dc">Dev Note</span>

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
	`haarcascade_frontalface_default` : 얼굴 인식을 위한 OpenCV 모델
	`shape_predicator_68_face_landmarks.dat` : 얼굴의 랜드마크를 예측하기 위한 dlib 모델
	`emotion_model.hdf5` : 감정을 예측하기 위한 미리 학습된 딥러닝 모델

### 코드 분석 및 테스트

**🔹Source Code**
	[FaceRecogRuntime-Python](FaceRecogRuntime-Python.md)
**🔹Custom Code**
	성능을 고려하고 5Frame 으로 화면이 갱신되도록 코드 수정
	[FaceRecogRuntimeCustom-Python](FaceRecogRuntimeCustom-Python.md)

### Python → Unity 통신

#### Socket 통신
**🔹Python**
	- TCP 소켓을 통해 Unity에 전송 (서버 역할)
	- 얼굴 인식 및 감정 데이터를 JSON 형식으로 변환
	- source code : [TCPEmotionSender-Python](TCPEmotionSender-Python.md)
**🔹Unity**
	- C#에서 TCP 클라이언트를 사용하여 데이터 수신 (클라이언트 역할)
	- JSON 데이터를 Unity의 객체로 파싱해 활용
	- source code : [TCPDataListener-Unity](TCPDataListener-Unity.md)


---
