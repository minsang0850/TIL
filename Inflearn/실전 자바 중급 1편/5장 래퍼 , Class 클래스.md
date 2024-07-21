# 래퍼 클래스 - 기본형의 한계
자바는 객체 지향 언어이지만, 자바 안에는 객체가 아닌 것이 있다.
바로 int, double 같은 기본형(Primitive Type)이다.

**한계**
- 개체가 아님: 객체 지향 프로그래밍의 장점을 살릴 수 없다. 예를 들어 객체는 유용한 메서드를 제공할 수 있는데,
기본형은 객체가 아니므로 메서드를 제공할 수 없다.
- null 값을 가질 수 없음

## 직접 만든 래퍼 클래스
특정 기본형을 감싸서 만든 클래스를 wrapper class라고 한다.
```java
public class IntWrapper {
    private final int value; // 불변

    public IntWrapper(int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }

    public int compareTo(int value) {
        if (this.value < value) {
            return -1;
        } else if (this.value == value) {
            return 0;
        } else {
            return 1;
        }
    }
    
    @Override
    public String toString() {
        return String.valueOf(value);
    }
}
```
- value라는 기본형 변수를 가지고 있음.
- 기본형 변수를 편리하게 사용하도록 다양한 메서드를 제공.
- 불변으로 설계

## 기본형과 null
기본형은 항상 값을 가져야 한다.
하지만 때로는 값이 없음을 표현해야 할 때가 있다.
이럴 때는 래퍼 클래스를 사용하면 된다.

# 자바 래퍼 클래스
- byte -> Byte
- short -> Short
- int -> Integer
- long -> Long
- float -> Float
- double -> Double
- char -> Character
- boolean -> Boolean

**래퍼 클래스의 특징**
- 불변이다.
- equals로 비교해야 한다.

- Integer.valueOf(10);
  - -128 ~ 127 사이의 값은 캐시에 저장되어 있음.

## 밗깅(Boxing)
- 기본형을 래퍼 클래스로 감싸는 것을 박싱이라고 한다.
- new Integer(10)은 직접 사용하면 안 된다. 작동은 하지만 , 향후 제거될 예정이다.
- 대신에 Integer.valueOf(10)을 사용하면 된다.

**intValue() - 언박싱(UnBoxing)**
- 래퍼 클래스에서 기본형 값을 얻는 것을 언박싱이라고 한다.
- intValue() 메서드를 사용하면 된다.

**래퍼 클래스의 비교**
- ==로 비교하면 안 된다. equals를 사용해야 한다.

# 오토 박싱
- 자바 5부터는 오토 박싱이라는 기능을 제공한다.
```java
Integer i = 10; // Integer.valueOf(10)으로 변환
int j = i; // i.intValue()로 변환
```

# 주요 메서드와 성능
- valueOf(): 래퍼 타입을 반환한다. 숫자, 문자열을 모두 지원한다.
- parseInt(): 문자열을 기본형으로 변환한다.
- compare(): 두 값을 비교한다.
- Integer.sum(), Integer.min(), Integer.max(): static 메서드로 간단한 연산을 수행한다.

**parseInt() vs valueOf()**
- valueOf("10")은 래퍼 타입을 반환한다.
- parseInt("10")은 기본형을 반환한다.

**래퍼 클래스의 성능**
- 단순 더하기만 해도 성능 차이가 좀 난다. (5배 정도..?)

## 기본형과 래퍼 클래스
- 기본형이든 래퍼 클래스든 이것을 1회로 환산하면 둘 다 매우 빠르게 연산이 수행된다.
- 일반적인 애플리케이션을 만드는 관점에서 보면 이런 부분을 최적화해도 사막의 모래알 하나 정도의 차이가 날 뿐이다.
- 그렇지 않은 일반적인 경우라면 코드를 유지보수하기 더 나은 것을 선택하면 된다.

### 유지보수 vs 최적화
- 최선 컴퓨터는 매우 빠르기 때문에 메모리 상에서 발생하는 연산을 몇 번 줄인다고해도 성능에 큰 영향을 미치지 않는다.
- 권장하는 방법은 개발 이후에 성능 테스트를 해보고 정말 문제가 되는 부분을 찾아서 최적화 하는 것이다.

# Class 클래스
Class 클래스는 클래스의 정보(메타데이터)를 다루는데 상요된다.

**주요 기능**
- 타입 정보 얻기: 클래스의 이름, 필드, 메서드, 생성자 정보를 얻을 수 있다.
- 리플렉션: 클래스에 정의된 메소드, 필드, 생성자 등을 조회하고, 이들을 통해 객체 인스턴스를 새엇ㅇ하거나 메소드를 호출하는 등의 작업을 할 수 있다.
- 동적 로딩과 생성: Class.forName() 메서드를 사용하여 클래스를 동적으로 로드하고, newInstance() 메서드를 통해 새로운 인스턴스를 생성할 수 있다.
- 애노테이션 처리: 클래스에 적용된 애노테이션(annotation)을 조회하고 처리하는 기능을 제공한다.

**조회 방식**
- 클래스 이름으로 조회: Class.forName("패키지명.클래스명")
- 인스턴스에서 조회: 객체.getClass()
- 클래스에서 조회: 클래스명.class

**getDeclaredConstructor().newInstance()**
- getDeclaredConstructor(): 생성자를 조회한다.
- newInstance(): 생성자를 기반으로 인스턴스를 생성한다.

### 리플렉션
Class를 사용하면 클래스의 메타 정보를 기반으로 메소드, 필드, 생성자 등을 조회하고, 이들을 통해 객체 인스턴스를 생성하거나 메소드를 호출하는 작업을 할 수 있다.

# System 클래스
- 시간 조회
- 환경 변수 조회
- 시스템 속성 조회
- System.arraycopy(originArray, srcPos, copiedArray, destPos, originArray.length): 배열 고속 복사
- System.exit(0): 프로그램 종료

# Math, Random 클래스
## Math 클래스
수학 공식 관련 메서드들을 제공한다.

## Random 클래스
랜덤의 경우 Math.random()을 사용해도 되지만 Random 클래스를 사용하면 더욱 다양한 랜덤값을 구할 수 있다.
참고로 Math.random()도 내부에서는 Random 클래스를 사용한다.

### 씨드
Random 은 내부에서 씨드(Seed) 값을 사용해서 랜덤 값을 구한다. 그런데 이 씨드 값이 같으면 항상 같은 결과가 출력된다.

- new Random(): 생성자를 비워두면 내부에서 System.nanoTime()에 여러가지 복잡한 알고리즘을 섞어서 생성한다. 따라서 반복 실행해도 결과가 항상 달라진다.
- new Random(int seed): 생성자에 씨드 값을 직접 전달할 수 있다.