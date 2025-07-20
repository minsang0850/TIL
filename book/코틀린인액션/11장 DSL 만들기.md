# 11.1 API에서 DSL로
클래스와 클래스 간 상호작용이 일어나는 연결 지점을 살펴봐야 한다. -> 클래스의 API를 살펴봐야 한다.
API가 깔끔하다.
- 코드를 읽는 독자들이 어떤 일이 벌어질지 명확하게 이해할 수 있어야 한다.
- 코드가 간결해야 한다.

깔끔한 APi를 돕는 코틀린 기능에는 확장 함수, 중위 함수 호출, 람다 구문에 사용할 수 있는 it 등의 문법적 편의, 연산자 오버로딩 등이 있다.

코틀린 DSL도 온전히 컴파일 시점에 타입이 정해진다.
따라서 컴파일 시점 오류 감지, IDE 지원 등 모든 정적 타입 지정 언어의 장점을 코틀린 DSL을 사용할 떄도 누릴 수 있다.

## 11.1.1 영역 특화 언어라는 개념
가장 익순한 DSL은 SQL과 정규식. 하지만 전체 애플리케이션을 정규식이나 SQL을 사용해 작성하는 경우는 없다.

DSL이 스스로 제공하는 기능을 제한함으로써 오히려 더 효율적으로 자신의 목표를 달성할 수 있다는 점을 생각해 보라.

DSL은 범용 프로그래밍 언어와 달리 더 선언적(declarative)이라는 점이 중요하다.
범용 프로그래밍 언어는 보통 명령적이다.
명령적 언어는 어떤 연산을 완수하기 위해 필요한 각 단계를 순서대로 정확히 기술하는 반면, 
선언적 언어는 원하는 결과를 기술하기만 하고 그 결과를 달성하기 위한 필요한 세부 실행은 언어를 해석하는 엔진에 맡긴다.
실행 엔진이 결과를 얻는 과정을 전체적으로 한번에 최적화하기 때문에 선언적 언어가 더 효율적인 경우가 자주 있다.

DSL에도 단점이 있다.
DSL을 범용 언어로 만든 호스트 애플리케이션과 함께 조합하기가 어렵다는 점이다.
이런 문제를 해결하며 DSL의 다른 이점을 살리기 위해 내부(internal) DSL이라는 개념이 유명해지고 있다.

## 11.1.2 내부 DSL
내부 DSL 은 범용 언어로 작성된 프로그램의 일부며, 범용 언어와 동일한 문법을 사용한다.

## 11.1.3 DSL의 구조
코틀린 DSL에서는 보통 람다를 중첩시키거나 메서드 호출을 연쇄시키는 방식으로 구조를 만든다.

DSL 구조의 장점은 같은 문맥을 함수 호출 시마다 반복하지 않고도 재사용할 수 있다는 점이다.
```kotlin
dependencies {
    compile("...")
    compile("...")
}
```

## 11.1.4 내부 DSL로 HTML 만들기
```kotlin
fun createSimpleTable() = createHtml().
    table {
        tr {
            td { +"cell"}
        }
    }
```
HTMl 텍스트를 작성하지 않고 코틀린 코드로 HTMl을 만들려는 이유?
코틀린 버전은 타입 안전성을 보장한다.

# 11.2 구조화된 APi 구축: DSL에서 수신 객체 지정 DSL 사용
## 11.2.1 수신 객체 지정 람다와 확장 함수 타입
```kotlin
fun buildString(
        builderAction: StringBuilder.() -> Unit
) : String = StringBuilder().apply(builderAction).toString()
```
StringBuilder는 수신 객체 타입.
람다에 전달되는 그런 타입의 객체를 수신 객체라고 부른다.

apply와 with는 모두 자신이 제공받은 수신 객체로 확장 함수 타입의 람다를 호출한다.

## 11.2.2 수신 객체 지정 람다를 HTML 빌더 안에서 사용

```kotlin
fun createSimpleTable() = createHtml().
    table {
        tr {
            td { +"cell"}
        }
    }
```
각 함수는 고차 함수로 수신 객체 지정 람다를 인자로 받는다.
여기서 관심을 가질 만한 것은 각 수신 객체 지정 람다가 이름 결정 규칙을 바꾼다는 점이다.
각 블록의 이름 결정 규칙은 각 람다의 수신 객체에 의해 결정된다.

```kotlin
open class Tag
class TABLE: Tag {
    fun tr(init: TR.() -> Unit)
}
class TR: Tag {
    fun td(init: TD.() -> Unit)
}
class TD: Tag
```
수신 객체를 묵시적으로 this 참조를 쓰지 않아도 되면 빌더 문법이 간단해지고 전체적인 구문이 원래의 HTML 구문과 비슷해진다.

## 11.2.3 코틀린 빌더: 추상화와 재사용을 가능하게 하는 도구

# 11.3 invoke 관례를 사용한 더 유연한 블록 중첩
invoke 관례를 사용하면 객체를 함수처럼 호출할 수 있다.

## 11.3.1 invoke 관례: 함수처럼 호출할 수 있는 객체
```kotlin
class Greeter(val greeting: String) {
    operator fun invoke(name: String) {
        println("$greeting, $name!")
    }
}
```

## 11.3.2 invoke 관례와 함수형 타입
각 함수형 인터페이스 안에는 그 인터페이스 이름이 가리키는 개수만큼 파라미터를 받는 invoke 메서드가 들어있다.

## 11.3.3 DSL의 invoke 관례: 그레이들에서 의존관계 정의
다음 두 형식을 모두 지원하고 싶다.
```kotlin
dependencies.compile("junit:junit:4.1.11")
dependencies {
    compile("junit:junit:4.1.11")
}
```

```kotlin
class DependencyHandler {
    fun compile(coordinate: String) {
        println("Added dependency on $coordinate")
    }
    
    operator fun invoke(
        body: DependencyHandler.() -> Unit) {
            body()
    }
}
```

# 11.4 실전 코틀린 DSL
## 11.4.1 중위 호출 연쇄: 테스트 프레임워크의 should
```kotlin
interface Matcher<T> {
    fun test(value: T)
}
class startWith(val prefix: String) : Matcher<String> {
    override fun test(value: String) {
        if (!value.startsWith(prefix))
            throw AssertionError
    }
}
```
```kotlin
"kotlin" should start with "kot"
// "kotlin".should(start).with("kot")
```

## 11.4.2 원시 타입에 대한 확장 함수 정의: 날짜 처리
```kotlin
val Int.days: Period
    get() = Period.ofDays(this)
```

## 11.4.3 멤버 확장 함수: SQL을 위한 내부 DSL
...