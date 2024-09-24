# 타입 매개변수 제한
개 병원은 개만 받고, 고양이 병원은 고양이만 받게.

```java
public class AnimalHospital<T extends Animal> {
    private T animal;
}
```


<T> 로 설정 시 Object가 제공하는 메서드만 호출 가능
-> Animal 타입의 메서드는 사용 불가, Animal이 아닌 타입도 전달 가능
-> <T extends Animal>로 설정하면 Animal의 메서드도 사용 가능 + Animal이 아닌 타입은 전달 불가

# 제네릭 메서드
특정 메서드에 제네릭을 적용

```java
public <T> void print(T t) {
    System.out.println(t);
}
```

**제네릭 타입**
- 정의: GenericClass<T>
- 타입 인자 전달: 객체를 생성하는 시점

**제네릭 메서드**
- 정의: <T> T genericMethod(T t)
- 타입 인자 전달: 메서드를 호출하는 시점
- 특정 메서드 단위로 제네릭을 도입할 때 사용
- 제네릭 메서드를 정의할 때는 메서드의 반환 타입 왼쪽에 다이아몬드를 사용해서 <T>와 같이 타입 매개변수를 적어준다.
- 제네릭 메서드는 메서드를 실제 호출하는 시점에 다이아몬드를 사용해서 <Integer> 와 같이 타입을 정하고 호출한다.

제네릭 타입은 static 메서드에 타입 매개변수를 사용할 수 없다.
제네릭 타입은 객체를 생성하는 시점에 타입이 정해진다.
그런데 static 메서드는 인스턴스가 아닌 클래스 단위로 작동하기 때문에 제네릭 타입과는 무관하다.
따라서 static 메서드에 제네릭을 도입하려면 제네릭 메서드를 사용해야 한다.

