`is` 키워드는 타입 검사를 수행하는 키워드다.
왼쪽의 값이 오른쪽의 타입과 호환되는지 확인하는 데 사용된다.
타입 검사 수행 후, 결과는 true 또는 false 로 반환한다.

### is 키워드 기본 사용법
타입 검사만 수행
`is` 키워드를 사용해 객체가 특정 타입인지 확인한다.(true/false 반환)
```csharp
object value = "Hello, World!";

if (value is string)
{
	Console.WriteLine("value는 문자열 타입입니다.");
}
else
{
	Console.WriteLine("value는 문자열 타입이 아닙니다.");
}
```
value 는 string 타입이기에 true가 반환되고 조건문이 실행된다.

### 패턴 매칭 (Pattern Matching)
패턴 매칭을 통한 타입 비교 및 변수 바인딩
타입을 비교한 다음, 조건이 true 
```csharp
if (enumId is EUIPage euiPage)
{
    Debug.Log($"EUIPage value: {euiPage}");
}
```
1. 타입 검사
	enumId 가 EUIPage 타입인지 확인한다.
2. 타입 바인딩
	타입 검사가 통과하면, enumId 값을 EUIPage 타입으로 변환하여 euiPage 변수에 저장한다.
3. 조건문 실행
	조건이 참인 경우 코드 블록이 실행된다.