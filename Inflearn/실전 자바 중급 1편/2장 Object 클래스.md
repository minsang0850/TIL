## java.lang 패키지
가장 기본이 되는 클래스들을 보관하는 패키지

- Object
- String
- Itneger, Long, Double
- Class: 클래스 메타 정보
- System: 시스템과 관련된 기본 기능들을 제공

**import 생략 가능**

## Object 클래스
모든 클래스의 최상위 부모 클래스

묵시적으로 Object 상송
- 묵시적: 개발자가 직접 기술하지 않아도 컴파일러에게 자동으로
- 명시적: 개발자가 직접 기술

### 자바에서 Object 클래스가 최상위 부모 클래스인 이유
- 공통 기능 제공
- 다형성의 기본 구현

**공통 기능 제공**
모든 객체에게 공통이 되는 기능을 정의 (네이밍 같은 컨벤션도 맞출 수 있음)

**다형성의 기본 구현**
부모는 자식을 담을 수 있다. 모든 객체를 참조할 수 있다.
모든 자바 객체는 Object 타입으로 처리될 수 있으며, 이는 다양한 타입의 객체를 통합적으로 처리할 수 있게 해준다.

## Object 다형성
**장점**
모든 객체를 Object로 쓸 수 있따.

**한계**
Object로 받은 객체는 Object의 메서드만 사용 가능하다. 
실제 객체의 메서드를 사용하려면 다운캐스팅이 필요하다.

### Object를 활용한 다형성의 한계
- Object는 모든 객체를 대상으로 다형적 참조를 할 수 있다.
- Object를 통해 전달 받은 객체를 호출하려면 각 객체에 맞는 다운캐스팅 과정이 필요하다.

다형성을 제대로 활용하려면 다형적 참조 + 메서드 오버라이딩을 함께 사용해야 한다.

다형적 참조는 가능하지만, 다형성을 활용하기에는 한계가 있다.

## Object 배열
Object 배열은 모든 객체를 담을 수 있다.

### Object가 없다면?
모든 객체를 받는 메서드나 배열을 만들 수 없다.

## toString
객체의 정보를 문자열 형태로 제공 -> 디버깅과 로깅에 유용하게 사용된다.
Object 클래스에 정의되므로 모든 클래스에서 상속받아 사용할 수 있다.

### Object.toString
패키지를 포함한 객체의 이름과 객체의 참조값(해시코드)를 16진수로 제공한다.
```java
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

### toString 오버라이딩
기본 toString은 객체의 상태를 적절히 나타내지 못한다.
그래서 보통 toString을 재정의(오버라이딩)
```java
@Override
public String toString() {
    return "Object 클래스의 toString 메서드를 재정의";
}
```

## Object와 OCP
### 추상적인 것에 의존
추상적인 Object 클래스에 의존한다.

- 다형적 참조: print(Object obj), Object 타입을 매개변수로 사용해서 다형적 참조를 사용한다.
- 메서드 오버라이딩: 구체 클래스들은 모두 Object가 가진 toString 메서드를 오버라이딩 할 수 있다. 런타임에 각 인스턴스의 toString()을 호출할 수 있다.

### OCP 원칙
System.out.println()을 사용하면 모든 객체의 정보(toString())를 편리하게 호출할 수 있다.

toString() 메서드와 같이, 자바 언어가 기본으로 제공하는 다양한 메서드들은 개발자가 필요에 따라 오버라이딩해서 사용할 수 있도록 설계되어 있다.

## equals() 
- 동일성(Identity): == 연산자를 사용해서 두 객체의 참조가 동일한 객체를 가리키는지 확인
- 동등성(Equality): equals() 메서드를 사용해서 두 객체가 논리적으로 동등한 객체인지 확인

equals를 재정의하지 않으면 동일성 비교를 기본으로 제공

**equals() 메서드를 구현할 때 지켜야 하는 규칙**
- 반사성(Reflexivity): 객체는 자기 자신과 동일하다.
  - x.equals(x)는 항상 true를 반환해야 한다.
- 대칭성(Symmetry): 두 객체가 동일하다면, 서로에 대해서도 동일해야 한다.
  - x.equals(y)가 true를 반환하면, y.equals(x)도 true를 반환해야 한다.
- 추이성(Transitivity): 두 객체가 동일하고, 두 번째 객체가 세 번째 객체와 동일하다면, 첫 번째 객체는 세 번째 객체와 동일해야 한다.
  - x.equals(y)가 true를 반환하고, y.equals(z)가 true를 반환하면, x.equals(z)도 true를 반환해야 한다.
- 일관성(Consistency): 객체의 상태가 변경되지 않는 한, equals() 메서드는 항상 같은 값을 반환해야 한다.
- null 비교: null 객체와 비교할 때는 항상 false를 반환해야 한다.
