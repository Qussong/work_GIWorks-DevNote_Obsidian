
### 상속 불가
- C#의 enum은 값 타입이며, 메모리와 성능 관점에서 가볍고 효율적인 구조로 설계되어 있다.
- enum은 특정한 기본타입(기본적으로 int)을 기반으로 하며, 더 이상 복잡한 계층 구조를 만들지 않는 것이 설계 철학이다.
- class 처럼 다형성이나 함수 오버라이딩 같은 기능은 제공되지 않는다.

### 제네릭을 사용한 Enum 타입 받기
`C# 7.3` 이상에서는 **`where T : Enum`** 제약 조건을 사용하여 특정 메서드가 enum 타입만 허용하도록 제한할 수 있다.
```csharp

public class EnumHandler
{
    public static void ProcessEnum<T>(T enumValue) where T : Enum
    {
        Console.WriteLine($"Enum Type : {typeof(T)}");
        Console.WriteLine($"Enum Value : {enumValue}");
    }
}

enum AnimalType
{
    Dog, Cat, Bird
}

enum ColorType
{
    Red, Green, Blue
}

public class Program
{
    static void Main(string[] args)
    {
        EnumHandler.ProcessEnum(AnimalType.Dog);
        EnumHandler.ProcessEnum(ColorType.Red);
        // [ 출력 결과 ]
        // Enum Type : AnimalType
		// Enum Value : Dog
		// Enum Type : ColorType
		// Enum Value : Red
    }
}
```

### Enum ↔ string
- `Enum.Parse` : 문자열을 Enum 값으로 변환할 때 사용한다. Enum 값으로 변환이 가능한 경우 Enum 값을 반환하고, 변환이 불가능한 경우 예외를 발생시킨다.
  문자열의 값은 Enum의 이름과 정확히 일치해야 하며, 대소문자를 구분해야한다. (대소문자를 구분하지 않으려면 `Enum.Parse` 호출 시 `true` 를 추가한다.)
- `Enum.ToString` : 
```csharp
class Program
{
    public enum ETest
    {
        HELLO,
        WORLD,
        NONE
    }

    static void Main(string[] args)
    {
        // Enum -> String
        string enumToString = ETest.HELLO.ToString();
        if (enumToString is string str)
        {
            Console.WriteLine(str); // HELLO
        }

        // String -> Enum
        ETest stringToEnum = (ETest)Enum.Parse(typeof(ETest), enumToString);
        if (stringToEnum is ETest e)
        {
            Console.WriteLine(e);   // HELLO
        }
    }
}
```