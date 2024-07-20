```java
String str = "Hello";
// String str = new String("Hello"); 처럼 동작. 실제로는 문자열 풀을 사용하긴 함
```

### String 클래스 구조
```java
public final class String {
    private final char[] value; //자바 9 이전
    private final byte[] value; // 자바 9 이후
}
```

실제 문자 데이터 자체는 char[]에 보관된다.

String 클래스는 개발자가 직접 다루기 불편한 char[]을 내부에 감추고 Stirng 클래스를 사용하는 개발자가 사용하기 편리하게 문자열을 다룰 수 있는 다양한 기능을 제공한다.

왜 자바 9부턴 byte 배열인가?

자바에서 char는 2byte, 그러나 영어, 숫자는 1byte만 사용 가능.
그렇지 않은 나머지 경우 2byte인 UTF-16 인코딩을 사용.


a.concat(b)와 a + b 는 동일하게 동작.

Java에서 '+' 연산을 String에 대해 허용

# String - 비교
String을 비교할 때는 ==이 아닌 equals 비교를 사용해야 합니다.

String 클래스에서 equals를 오버라이딩해서 값을 비교하도록 구현해놨음.

```java
String str1 = "hello";
String str2 = "hello";
System.out.println(str3==str4) // true;
```

문자열 리터럴을 사용하는 경우 자바는 메모리 효율성과 성능 최적화를 위해 문자열 풀을 사용한다.  
자바는 실행되는 시점에 클래스에 문자열 리터럴이 있으면 문자열 풀에 String 인스턴스를 미리 만들어둔다.  
같은 문자를 사용하는 경우 메모리 사용을 줄이고 문자를 만드는 시간도 줄기 때문에 성능도 최적화할 수 있다.

# String - 불변 객체
String은 불변 객체이다. 생성 이후에 문자열 값을 변경할 수 없다.
concat이나 + 연산을 사용하면 새로운 문자열을 생성(기존 문자열은 그대로)

## 불변으로 설계된 이유
**가변으로 인한 사이드이펙트**
문자열 풀에 있는 String 인스턴스의 값이 중간에 변경되면 같은 문자열을 참고하는 다른 변수의 값도 함께 변경될 수 있다.

# String - 메서드
### 문자열 정보 조회
- length(): 길이
- isEmpty(): 비어 있는지
- isBlank(): 비어 있거나 공백인지
- charAt(int index): 저장된 인덱스에 있는 문자 반환

### 문자열 비교
- equals(Object anObject): 두 문자열이 동일한지 비교한다.
- compareTo(String string): 두 문자열을 사전 순으로 비교한다.
- startsWith(String prefix): 문자열이 특정 접두사로 시작하는지 확인한다.
- endsWith(String suffix): 문자열이 특정 접미사로 끝나는지 확인한다.

### 문자열 검색
- contains(CharSequence s): 문자열이 특정 문자열을 포함하고 있는지 확인한다.
- indexOf(String ch): 문자열이 처음 등장하는 위치를 반환한다.

### 문자열 조작 및 변환
- SubString(int beginIndex): 문자열의 부분 문자열을 반환한다.
- concat(String str): 문자열의 끝에 다른 문자열을 붙인다.
- replace(CharSequence target, CharSequence replacement): 특정 문자열을 새 문자열로 대체한다.
- toLowerCase(): 소문자로 변환
- trim(): 문자열 양쪽 끝의 공백을 제거한다. 단순 Whitespace만 제거할 수 있다.

### 문자열 분할 및 조합
- sprint(String regex): 문자열을 정규 표현식을 기준으로 분할한다.
- join(CharSequence delimiter, CharSequence... elements): 주어진 구분자로 여러 문자열을 결합한다.

### 기타 유틸리티
- valueOf(Object obj): 다양한 타입을 문자열로 변환한다.
- toCharArray(): 문자열을 문자 배열로 변환한다.

# StringBuilder - 가변 String
**더하기 연산 동작 방식**
- "A" + "B"
- String("A") + String("B")
- String("A").concatString("B"))
- new String("AB")

n개의 문자열을 더하면 n-1개의 String 클래스가 추가로 생성된다. 

문자를 자주 더하거나 변경해야 하는 상황이라면 더 많은 String 클래스를 만들고, GC 해야 한다.
결과적으로 리소스를 더 많이 사용하게 된다.

## StringBuilder
StringBuilder는 가변 String
```java
StringBuilder sb = new StringBuilder();
sb.append("A");
sb.append("B");
sb.append("C");
sb.append("D");
String completed = sb.toString();
```

# String - 최적화
## 자바의 String 최적화
자바 컴파일러는 문자열 리터럴을 더하는 부분을 자동으로 합쳐준다.

**문자열 리터럴 최적화**
- 컴파일 전: String helloWorld = "Hello, " + "World!";
- 컴파일 후: String helloWorld = "Hello, World!";

**String 변수 최적화**
- String result = str1 + str2;
- String result = new StringBuilder().append(str1).append(str2).toString();

자바가 최적화를 처리해주기 때문에 간단한 경우에는 StringBuilder를 사용하지 않아도 된다.
(자바 9부터는 StringConcatFactory를 사용해서 최적화를 수행한다.)

**최적화가 어려운 경우**
```java
for(int i=0; i<100000; i++) {
    result += "Hello Java";
}

for(int i=0; i<100000; i++) {
    result += new StringBuilder().append(result).append("Hello Java").toString());
}
```
컴파일러는 얼마나 많은 반복이 일어날지, 예측이 어려우니 최적화가 어렵다.
StringBuilder는 물론이고, 반복 횟수만큼 n 번의 String 객체를 생성했을 것이다.

### StringBuilder를 직접 사용하는 것이 더 좋은 경우
- 반복문에서 반복해서 문자를 연결할 때
- 조건문을 통해 동적으로 문자열을 조합할 때
- 복잡한 문자열의 특정 부분을 변경해야 할 때
- 매우 긴 대용량 문자열을 다룰 때

### StringBuilder vs StringBuffer
- StringBuilder: 멀티 스레드 상황에 안전하지 않지만, 동기화 오버헤드가 없으므로 속도가 빠르다.
- StringBuffer: 내부에 동기화가 되어 있어서, 안전하지만 동기화 오버헤드로 인해 성능이 느리다.

# 메서드 체이닝 - Method Chaining
메서드 체이닝 기법은 코드를 간결하고 읽기 쉽게 만들어 준다.



