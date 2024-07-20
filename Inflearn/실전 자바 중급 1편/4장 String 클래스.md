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