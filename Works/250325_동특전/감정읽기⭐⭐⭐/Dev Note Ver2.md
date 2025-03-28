# 아이디어

---

# 학습 내용

### <span style="background:lightgray">파일 입출력</span>

#### 경로
- `Application.persistentDataPath`
	해당 경로는 Unity 프로젝트 외부의 쓰기 가능한 경로를 제공한다.
	해당 경로에 저장된 파일은 Unity가 관리하지 않기에 `.meta` 파일이 생성되지 않는다.

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

### <span style="background:lightgray">경로전달</span>

#### source code (unity)

### source code (python)

#### output
