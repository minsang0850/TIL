# Map 소개
Map은 키-값의 쌍을 저장하는 자료 구조이다.
- 키는 맵 내에서 유일해야 한다.
    - 같은 키에 다시 저장하면 덮어씌워진다.
- 키는 중복될 수 없지만, 값은 중복될 수 있다.
- Map은 순서를 유지하지 않는다.

Map은 별도의 인터페이스가 있음.

keySet(실제로 Set 자료구조)를 받을 수 있음.
Values(Collection 자료구조)를 받을 수 있음.

# Map 구현체
- HashMap
- TreeMap
- LinkedHashMap

Map의 Key는 Set 구조. 그래서 구현체도 유사.
Map과 Set의 key에 value를 가지고 있느냐의 차이다.

# 스택 자료 구조
위쪽만 열려있는 통에 데이터를 넣고 빼는 것

Java의 Stack 클래스는 Vector 클래스를 상속받아 구현되어 있음.
Vectore 클래스는 자바 1.0에 개발됐는데 지금은 사용되지 않고, 하위 호환을 위해 존재한다.
따라서 Vector를 사용하는 Stack도 사용하지 않는 것을 권장한다.

# 큐 자료 구조
선입선출 자료구조
큐에 넣는 것을 offer, 빼는 것을 poll이라고 한다.

- Queue의 인터페이스는 List, Set과 같이 Collection의 자식이다.
- Queue의 대표적인 구현체는 ArrayDeque, LinkedList가 있다.

# Deque 자료 구조
Double Ended Queue의 약자이다.
일반적인 Queue와 Stack의 기능을 모두 가지고 있다.

ArrayDeque가 LinkedList보다 훨씬 빠름.
ArrayDeque는 원형 큐 자료구조를 사용한다.

# Deque 와 Stack, Queue
Deque로 Stack, Queue를 구현할 수 있다.
