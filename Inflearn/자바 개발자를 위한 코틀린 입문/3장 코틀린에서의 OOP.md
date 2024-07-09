## 코틀린에서 클래스를 다루는 방법
### 클래스와 프로퍼티

```kotlin
//public이 default
class Person(val name: String, var age: Int){
    init {
        if(age < 0) {
            age = 0
        }
    }
    
    constructor(name: String) : this(name, 0){
        print("부생성자 1")
    }
    
    //프로퍼티처럼 선언
    val isAdult: Boolean
        get() = age >= 20
}
```
- 코틀린은 필드만 만들면 getter, setter를 자동으로 만들어줌
  - '.'필드로 바로 사용 가능
  - Java 필드를 가져와도 사용 가능
- constructor는 생략 가능
- 생성자에서 프로퍼티를 만들 수 있음
- body(block)도 비어있으면 생략 가능
- init 블록
  - 생성자가 호출되는 시점에 호출
- 부생성자가 있지만 default 파라미터를 권장
  - 혹은 정적 팩토리 메서드 추천

### 커스텀 getter
```kotlin
//public이 default
class Person(
    name: String
){
    val name: String = name
        get() = return field.uppercase()
}
```

## 코틀린에서 상속을 다루는 방법
상속을 받을 때 콜론(:)을 사용, 인터페이스 구현 시에도 동일
```kotlin
class Cat(
        species: String
) : Animal(species, 4) { // :을 한 칸 띄워줘야 상속으로 사용
    
    override fun move() { // override를 필수로 붙여줘야 함
        println('고양이가 걸어가')
    }
}
```

```kotlin
class Penguin(
        species: String
) : Animal(species, 2), Swimable { 
    override fun move() {
        println('펭귄이 걸어가')
    }
  
    override fun swim() {
        super<Swimable>.swim()
    }
}
```

상위 클래스에서 하위 클래스가 override 하고 있는 프로퍼티를 생성자 블록이나  init 블록에서 사용하면 위험

- final: override를 할 수 없게 한다. default로 보이지 않게 존재
- open: override를 열어 줌
- abstract: 반드시 override
- override: 필수

## 코틀린에서 접근 제어를 다루는 방법
### Java와 코틀린의 가시성 제어
- Java: public, protected, default, private
- 코틀린: public, protected, internal, private
  - internal: 같은 모듈 내에서만 접근 가능
  - protected: 상속 관계에서만 접근 가능
  - private: 같은 파일 내에서만 접근 가능

.kt 파일 하나에 변수, 함수, 클래스를 여러 개 선언할 수 있음

### 코틀린 파일의 접근 제어
- public: 모든 곳에서 접근 가능
- internal: 같은 모듈 내에서만 접근 가능
- private: 같은 파일 내에서만 접근 가능
- protected: 파일(최상단)에는 사용 불가능

### 다양한 구성요소의 접근 제어
생성자에 접근지시어를 붙이려면 constructor 명시

Java에선 Kotlin 모듈의 internal 코드를 가져올 수 있다.

## 코틀린에서 Object 키워드를 다루는 법
### static
```kotlin
class Person private constructor(
        var name: String,
        var age: Int,
) {
    @JvmStatic //바로 접근 가능.
    companion object {
        const private val MIN_AGE = 0 //const val로 선언하면 static final로 선언됨 - 컴파일 시에 할당
        fun newBaby(name: String): Person {
            return Person(name, MIN_AGE)
        }
    }
}
```

### 싱글톤
앞에 object만 붙여주면 끝
```kotlin
object Singleton {
    fun doSomething() {
        println("싱글톤 객체")
    }
}
```

### 익명 클래스
object : 타입 {} 로 익명클래스 표현

## 코틀린에서 중첩 클래스를 다루는 방법
중첩 클래스는 기본적으로 static 클래스
```kotlin
class Outer {
    class Nested {
        fun doSomething() {
            println("중첩 클래스")
        }
    }
}
```

## 코틀린에서 다양한 클래스를 다루는 방법
### Data Class
- data 키워드를 붙여주면 equals, hashCode, toString, copy 메서드를 자동으로 생성
- componentN 메서드를 자동으로 생성

```kotlin
data class PersonDto(
    val name: String,
    val age: Int,
)
```

### Enum Class
- enum 키워드를 붙여주면 enum 클래스로 생성

```kotlin
enum class Color (
        private val code: String
){
    RED, GREEN, BLUE
}
```
- when을 사용해서 쉽게 분기 처리 가능
- 컴파일러가 Enum의 변화를 체크

### Sealed Class, Sealed Interface
- 컴파일 타임 때 하위 클래스의 타입을 모두 기억한다. 즉 런타임때 클래스 타입이 추가될 수 없다.
- 하위 클래스는 같은 패키지에 있어야 한다.
- Enum과의 차이
  - 클래스를 상속받을 수 있다.
  - 하위 클래스는 멀티 인스턴스가 가능하다.