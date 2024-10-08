# 리스트 추상화
특정 구체 클래스에 의존하게 되면 변경이 일어날때마다 의존하는 클래스도 함께 수정해야 한다.
-> 인터페이스(List 인터페이스 같은)를 의존하면 해결

ArrayList는 요소들이 메모리 상에서 연속적으로 위치하여 CPU 캐시 효율이 좋고, 메모리 접근 속도가 빠르다.

## Colllection 인터페이스
java.util 패키지의 컬렉션 프레임워크의 핵심 인터페이스 중 하나이다.
이 인터페이스는 자바에서 다양한 컬렉션을 다루기 위한 메서드를 정의한다.

## Java ArrayList
java.util.ArrayList

**특징**
- 기본 Capacity는 10
- 배열을 사용해서 데이터를 관리
- 메모리 고속 복사 연산을 사용한다.
  - 시스템 레벨에서 최적화된 메모리 고속 복사 연산을 사용해서 비교적 빠르게 수행된다.
  - 대략 O(n/10)

## Java LinkedList
- 이중 연결 구조 (prev + next)
- 마지막 노드에 대한 참조를 제공한다.