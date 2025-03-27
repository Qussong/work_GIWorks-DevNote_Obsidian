# <span style="background:#f5f5dc">Content</span>

### 기획 및 내용
| Zone    | Corner | Item | No    | 연출개요                                 | 예상난이도 |
| ------- | ------ | ---- | ----- | ------------------------------------ | ----- |
| Zone 02 | 뇌와감정   | 감정읽기 | 3-1-3 | 카메라를 통해 자신의 표정을 인식하여 <br>감정을 분석하는 체험 | ⭐⭐    |
![](감정읽기-세부연출계획.png)

---
# <span style="background:#f5f5dc">Install & Repair</span>
### N회차
Date : 
Memo :
	- !!!
	- !!!

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

### Socket 통신 (Python → Unity)

파이썬 코드로 웹캠에 접근해 얼굴에 대한 정보를 얻고, 이를 통해 분석한 데이터를 Unity로 전송한다.
#### Source Code
source code : [FaceRecogRuntime-Python](FaceRecogRuntime-Python.md)
웹캠을 통해 얼굴을 인식하고 표정을 분석하는 코드
#### 코드 분석 및 테스트
**🔹Python**
	source code : [TCPEmotionSender-Python](TCPEmotionSender-Python.md)
	- TCP 소켓을 통해 Unity에 전송 (서버 역할)
	- 얼굴 인식 및 감정 데이터를 JSON 형식으로 변환
	- 성능을 고려하고 5Frame 으로 화면이 갱신되도록 수정
**🔹Unity**
	source code : [TCPDataListener-Unity](TCPDataListener-Unity.md)
	- C#에서 TCP 클라이언트를 사용하여 데이터 수신 (클라이언트 역할)
	- JSON 데이터를 Unity의 객체로 파싱해 활용
🔹 **패킷 통신**
	패킷 통신은 데이터를 작은 패킷 단위로 나누어 네트워크를 통해 전송하는 방식이다. 각 패킷에는 데이터(Payload)뿐만 아니라 수신자 정보를 포함한 헤더(Header)가 포함되어 있어야 하며, 목적지에서 패킷을 조립하여 원본 데이터로 복원된다.

### 파일 입출력

#### 경로
🔹**Application.persistentDataPath**
	해당 경로는 Unity 프로젝트 외부의 쓰기 가능한 경로를 제공한다.
	해당 경로에 저장된 파일은 Unity가 관리하지 않기에 `.meta 파일이 생성되지 않는다.

---
