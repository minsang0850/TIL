## 코틀린에서 배열과 컬렉션을 다루는 방법
### 배열
잘 쓰지 않긴 함
```kotlin
val array = arrayOf(100, 200)
for(i in array.indices)
    println(array[i])
```

### 컬렉션
컬렉션이 불변인지, 가변인지 설정해야 함.
- 가변 컬렉션: MutableList, MutableSet, MutableMap, element 추가/삭제 가능, but 요소 값 변경은 가능
- 불변 컬렉션: List, Set, Map, element 추가/삭제 불가능

**List**
```kotlin
val list = listOf(1, 2, 3) // 불변 리스트
val mutableList = mutableListOf(1, 2, 3) // 가변 리스트

val emptyList = emptyList<Int>() // 타입 추론 가능하면 꺽새 빼도 괜찮
for(i in list)
    println(i)
for ((index, value) in list.withIndex())
    println("index: $index, value: $value")
```
mutableList는 arrayList로 만들어짐.

**Set**
```kotlin
val set = setOf(1, 2, 3) // 불변 셋
var mutableSet = mutableSetOf(1, 2, 3) // 가변 셋
```
mutableSet은 LinkedHashSet으로 만들어짐.

**Map**
```kotlin
val map = mapOf(1 to "one", 2 to "two") // 불변 맵
val mutableMap = mutableMapOf(1 to "one", 2 to "two") // 가변 맵

for((key, value) in map)
    println("key: $key, value: $value")
```
mutableMap은 LinkedHashMap으로 만들어짐.

**컬렉션의 null 가능성**
- List<Int?> : 리스트에 null이 들어갈 수 있지만, 리스트는 null이 아님
- List<Int>? : 리스트 자체가 null일 수 있음
- List<Int?>?: 리스트가 null이거나 리스트 안에 null이 들어갈 수 있음

**Java와 함께 사용** 
- 불변 collection을 구변할 수 없으므로 위험.
- 요소에 null이 들어갈 수 있음(막아놔도)

## 코틀린에서 다양한 함수를 다루는 방법
### 확장함수
기존 클래스에 새로운 함수를 추가할 수 있음
```kotlin
fun String.lastChar(): Char = this.get(this.length - 1)
```
확장함수는 클래스에 있는 private, protected 멤버에 접근 불가능

멤버함수와 확장함수의 시그니처가 같다면?
- 멤버함수가 우선권을 가짐

```kotlin
open class Train(
        val name: String = "새마을기차",
        val price: Int = 10000,
)

fun Train.isExpensive(): Boolean = price > 10000

class Srt : Train("SRT", 50000)

fun Srt.isExpensive(): Boolean = price > 50000
```

```kotlin
val train: Train = Train()
train.isExpensive() // Train의 확장함수

val srt1: Train = Srt()
srt1.isExpensive() // Train의 확장함수

val Srt2: Srt = Srt()
srt2.isExpensive() // Srt의 확장함수
```

자바에서는 코틀린의 확장함수를 정적 함수처럼 사용할 수 있다.

확장함수라는 개념은 확장프로퍼티와 연결 -> 확장 프로퍼티의 우너리는 확장함수 + custom getter

### infix 함수(중위 함수)
```kotlin
infix fun Int.plus(x: Int): Int = this + x

val result = 1 plus 2
```

### inline 함수
함수 호출 시 함수의 내용을 복사해서 호출하는 방식

함수를 파라미터로 전달할 때에 오버헤드를 줄일 수 있다.

### 지역함수
함수 내부에 선언된 함수
```kotlin
fun foo() {
    fun bar() {
        println("bar")
    }
    bar()
}
```

## 코틀린에서 람다를 다루는 방법
코틀린에서는 함수가 그 자체로 값이 될 수 있다.

```kotlin
val isApple = fun(fruit: Fruit): Boolean {
    return fruit.name == "apple"
}

val isApple2 = { fruit: Fruit -> fruit.name == "apple" }

//사용법
isApple(Fruit("apple"))
isApple.invoke(Fruit("apple"))

fun fileerFruits(fruits: List<Fruit>, predicate: (Fruit) -> Boolean): List<Fruit> {
    val filteredFruits = mutableListOf<Fruit>()
    for(fruit in fruits) {
        if(predicate(fruit)) {
            filteredFruits.add(fruit)
        }
    }
    return filteredFruits
}

fileerFruits(fruits) { fruit -> fruit.name == "apple" }
//매개변수가 하나면 it으로 대체 가능
fileerFruits(fruits) { it.name == "apple" }
```

### Closure
Java에서는 람다를 쓸 때 사용할 수 있는 변수에 문제가 있다.

kotlin은 아님.
람다가 시작하는 시점에 참조하고 있는 모든 변수를 참조.
이렇게 해야만, 람다를 진정한 일급 시민으로 간주할 수 있다.
이러한 데이터 구조를 Closure 라고 한다.

## use
에서 람다 사용

## 코틀린에서 컬렉션을 함수형으로 다루는 방법
```kotlin
val apples = fruits.filter { it.name == "apple" }
val applesWithPrintIndex = fruits.filterIndexed { index, fruit -> fruit.name == "apple" }
```

**여러 가지 기능**
- all: 전체 만족 여부
- any: 하나라도 만족 여부
- none: 만족하는 요소가 없는지
- count: 만족하는 요소의 개수
- find: 만족하는 첫 번째 요소 
- first: 만족하는 첫 번째 요소 (null이 아니어야 함)
  - firstOrNull: null이어도 됨
- last: 만족하는 마지막 요소
- sortedBy: 정렬
- distinctBy: 중복 제거

**List to Map**
```kotlin

val fruitsMap = fruits.groupBy { it.name }

val fruitsMap2 = fruits.associateBy { it.name }
```