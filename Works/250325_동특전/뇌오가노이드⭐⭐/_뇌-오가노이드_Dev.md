# <span style="background:#f5f5dc">Content</span>

### 기획 및 내용
| Zone    | Corner   | Item                            | No        | 연출개요                                                                        | 예상난이도 |
| ------- | -------- | ------------------------------- | --------- | --------------------------------------------------------------------------- | ----- |
| Zone 03 | 뇌 과학의 미래 | 뇌 오가노이드 -<br>최신 뇌 과학에 대한 생각 나누기 | 4-3-2-(3) | BCI, 뇌 오가노이드, 인공지능등최신뇌과학기술에대한생각을키오스 크를통해정리해보고, 집계된결과로다른사람들의생각을확인해보는 전시마무리체험 | ⭐     |
![](뇌-오가노이드-세부연출계획.png)
![500](temp_1743988996774.png)

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
	- 파일 입출력 (JSON)

### JSON 입출력

#### JsonUtility
유니티에서 제공하는 JSON 파서지만 기능이 다양하지 않다.
- 클래스 타입만 직렬화 가능
- 속성이 public 이거나 SerializeField 가 있어야 함
- JSON 포맷이 단순한 경우만 직렬화하기 적절함
```csharp
using UnityEngine;

[System.Serializable]
public class PlayerData
{
    public string name;
    public int score;
}

public class JsonExample
{
    public static string Serialize(PlayerData data)
    {
        return JsonUtility.ToJson(data, true); // JSON 변환
    }

    public static PlayerData Deserialize(string json)
    {
        return JsonUtility.FromJson<PlayerData>(json);
    }
}

// 사용 예제
PlayerData player = new PlayerData { name = "Hero", score = 100 };
string json = JsonExample.Serialize(player);
Debug.Log(json);

PlayerData loadedPlayer = JsonExample.Deserialize(json);
Debug.Log($"Loaded Player: {loadedPlayer.name}, Score: {loadedPlayer.score}");
```

🔹**ToJson(object,bool)**
	`직렬화(Serialization)`는 객체나 데이터 구조를 파일, 메모리, 네트워크 전송 등을 통해 저장하거나 전달하기 위해 이진 또는 텍스트 형식으로 변환하는 과정을 의미한다.
```csharp
public static string ToJson(object obj, bool prettyPrint);
```
	 
🔹**FromJson<T\>(string)**
	`역직렬화(Deserialization)`는 직렬화된 데이터를 다시 원래의 객체나 데이터 구조로 복원하는 과정을 의미한다.
```csharp
public static T FromJson<T>(string json);
```
#### Newtonsoft.Json
유니티에서 사용가능한 가장 강력한 JSON 라이브러리
- Dictionary, List 등 다양한 자료형 지원
- private 변수도 직렬화 가능 (JsonProperty 활용)
- JsonUtility 보다 유연하고 강력한 기능 제공




---
