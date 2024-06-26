### 단계가 얼마나 커야 하나?
수작업 리팩토링은 에러가 발생하기 쉽기 때문에 -> 리팩토링 초기에는 아주 작은 단계로 작업할 준비가 되어 있어야 한다.

### 테스트가 필요 없는 것은 무엇인가?
"두려움이 지루함으로 변할 때까지 테스트를 만들어라."

하지만 내가 작성하는 것들에 대해서만 테스트해라.

### 좋은 테스트를 갖췄는지의 여부를 어떻게 알 수 있는가?
테스트란 탄광 속에서 자신의 고통을 통해 고약한 설계 가스의 존재를 드러내는 카나리아다.

**설계 문제가 있음을 알려주는 테스트의 속성**
- 긴 셋업 코드: 객체가 너무 크다는 뜻이므로 나뉠 필요가 있다.
- 셋업 중독: 공통의 셋업 코드를 넣어 둘 공통의 장소를 찾기 힘들다면, 서로 밀접하게 엉킨 객체가 너무 많다는 뜻이다.
- 실행 시간이 오래 걸리는 테스트: 테스트하기가 힘듦.
- 깨지기 쉬운 테스트: 예상치 못하게 실패하는 테스트는 애플리케이션의 특정 부분이 다른 부분에 이상한 방법으로 영향을 끼친다는 뜻이다.

### TDD로 프레임워크를 만들려면 어떻게 해야 하나?
변화를 매우 빨리 연달아 도입하는 극한적 상황에서 TDD는 미리 설계하고 개발하는 것처럼 보인다.

TDD를 오래한다면 대부분의 누군가의 실수를 알아채기도 전에 재빨리 고칠 수 있다.

### 피드백이 얼마나 필요한가?
테스트를 얼마나 작성할지 고려할 때, 실패간 평균시간(MTBF, Mean Time Between Failures)를 생각해야 한다.

TDD의 테스트는 어떤 목적(신뢰할 수 있는 코드)을 위한 하나의 수단이다.

### 테스트를 지워야 할 때는 언제인가?
중복 테스트 제거의 기준?
- 테스트를 삭제할 경우 자신감이 줄어들 것 같으면 테스트를 지우지 말아야 한다.
- 두 개의 테스트가 코드의 동일한 부분을 실행하더라도, 이 둘이 서로 다른 시나리오를 말한다면 그대로 남겨두어야 한다.

### 프로그래밍 언어나 환경이 TDD에 어떤 영향을 주는가?

### 거대한 시스템을 개발할 때에도 TDD를 할 수 있는가?
시스템에 있는 기능의 양은 TDD의 효율에 영향을 미치지 않는 것 같다.

### 애플리케이션 수준의 테스트로도 개발을 주도할 수 있는가?
작은 규모의 테스트로 개발을 주도하는 것의 문제는 실제로 사용자가 원하지 않는데 그들이 원할 거라고 생각하고 구현할 수도 있는 위험을 끌고 간다는 점에 있다.

보통 프로그래머 수준의 TDD를 원하게 된다.
- 즉시 초록 막대를 볼 수 있고
- 내부 설계를 단순화할 수 있도록 할 수 있길 원한다.

### 프로젝트 중반에 TDD를 도입하려면 어떻게 해야 할까?
하지 말아야 할 일.
코드 전체를 위한 테스트를 한꺼번에 다 만들고, 코드 전체를 한번에 리팩토링 하는 일이다.

우선 해야 할 일은 변겨으이 범위를 제한하는 것이다.
다음으로 해야 할 일은 테스트와 리팩토링 사이에 존재하는 교착상태(deadlock)을 풀어주는 것이다.

### TDD는 누구를 위한 것인가?
TDD는 더 깔끔한 설계를 할 수 있도록, 그리고 더 많은 것을 배워감에 따라 설계를 더 개선할 수 있도록, 적절한 때 적절한 문제에 집중할 수 있게끔 도와준다.

### TDD는 초기 조건에 민감한가?

### TDD와 패턴의 관계는?
TDD와 패턴의 또 다른 관계는 패턴 주도 설계(pattern-driven design)에 대한 구현 방법으로써의 TDD다.

그냥 시스템이 무슨 일을 할지 생각하고 나중에 설계가 알아서 정해지도록 하는 것이 더 낫다.

### 어째서 TDD가 잘 작동하는가?
TDD는 변화에 안정감을 부여해준다.

TDD의 또 다른 효과는 설계 결정에 대한 피드백 고리를 단축시킨다는 점이다.

### 이름을 테스트 주도 개발이라고 한 이유는?
개발을 테스트로 주도하지 않으면 무엇으로 주도할 것인가?

### TDD와 익스트림 프로그래밍의 실천법 사이에 어떤 관련이 있는가?
**익스트림 프로그래밍**
- 조금씩, 하지만 자주 발표한다.
- 사이클을 반복해서 돌리면서 개발한다.
- 스펙에 없는 것은 절대 집어넣지 않는다.
- 테스트 코드를 먼저 만든다.
- 야근은 하지 마라. 항상 정규 일과 시간에만 작업한다.
- 기회가 생기는 족족 언제 어디서든 코드를 개선한다.
- 모든 테스트를 통과하기 전에는 어떤 것도 발표하지 않는다.
- 조금씩 발표하는 것을 기반으로 하여 현실적인 작업 계획을 만든다.
- 모든 일을 단순하게 처리한다.
- 두 명씩 팀을 편성하고 모든 사람이 대부분의 코드를 알 수 있도록 돌아가면서 작업한다.


TDD를 하면서 작성하게 되는 테스트는 짝 프로그래밍 과정에서 뛰어난 의사소통 수단이 된다.

TDD -> 설계를 단순하게 만들어줌.

