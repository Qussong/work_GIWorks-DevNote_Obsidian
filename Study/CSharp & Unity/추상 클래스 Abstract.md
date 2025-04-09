
- 추상 클래스는 부분적으로 구현된 클래스
- 추상 메서드와 일반 메서드를 모두 가질 수 있다.
- 상태값(필드, 변수)을 가질 수 있으며, 상속받는 클래스에서 이를 사용할 수 있다.
- 인터페이스와 달리 다중 상속이 불가능하다.

### 사용 목적
1. 코드 재사용
	여러 클래스에서 공통된 구현을 제공하며, 필요한 부분만 자식 클래스가 재정의하도록 한다.
2. 공통 기반 제공
	상속 구조에서 기본적인 필드와 메서드 구현을 정의하여 코드 중복을 줄인다.

### 예시 코드
```csharp
public interface IAnimal
{
    void Speak();
}

public abstract class Animal : IAnimal
{
    public string Name {get; set;}
    public void Sleep() { Console.WriteLine($"{Name} is sleeping.");}

    public abstract void Speak();
}

public class Dog : Animal
{
    public override void Speak()
    {
        Console.WriteLine("Woof!");
    }
}
```

- `IAnimal` 인터페이스를 상속하는 `Animal` 추상 클래스는 Speak() 함수를 무조건 구현할 필요는 없지만, 구현하지 않을 경우 반드시 추상 메서드로 선언해야한다.
