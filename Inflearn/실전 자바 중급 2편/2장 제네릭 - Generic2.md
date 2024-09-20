# 타입 매개변수 제한
개 병원은 개만 받고, 고양이 병원은 고양이만 받게.

```java
public class AnimalHospital<T extends Animal> {
    private T animal;
}
```


<T> 로 설정 시 Object가 제공하는 메서드만 호출 가능
-> Animal 타입의 메서드는 사용 불가, Animal이 아닌 타입도 전달 가능
-> <T extends Animal>로 설정하면 Animal의 메서드도 사용 가능
