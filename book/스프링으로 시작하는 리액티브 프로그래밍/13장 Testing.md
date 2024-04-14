## 13.1 StepVerifier를 사용한 테스팅
Reactor에서 가장 일반적인 테스트 방식은 Flux 또는 Mono를 Reactor Sequence로 정의한 후, 구독 시점에 해당 Operator 체인이 시나리오 대로 동작하는지를 테스트하는 것입니다.

**Signal 이벤트 테스트**
StepVerifier를 이용한 가장 기본적인 테스트 방식은 바로 Reactor Sequence에서 발생하는 Signal 이벤트를 테스트하는 것입니다.


- create()을 통해 테스트 대상 Sequence를 생성합니다.
- expectXXXX()를 통해 Sequence에서 에상되는 Signal의 기댓값을 평가합니다.
- verify()를 호출함으로써 전체 Operator 체인의 테스트를 트리거합니다.

**expectXXXX() 메서드**
- expectSubscription(): 구독이 이루어짐을 기대한다.
- expectNext(T t): onNext Signal을 통해 전달되는 값이 파라미터로 전달된 값과 같음을 기대한다.
- expectComplete(): onComplete Signal이 전송되기를 기대한다.
- expectError(): onError Signal이 전송되기를 기대한다.
- expectNextCount(long count): 구독 시점 또는 이전 expectNext()를 통해 기댓값이 평가된 데이터 이후부터 emit된 수를 기대한다.
- expectNoEvent(Duration duration): 주어진 시간 동안 Signal 이벤트가 발생하지 않았음을 기대한다.
- expectAccessibleContext(): 구독 시점 이후에 Context가 전파되었음을 기대한다.
- expectNextSequence(Iterable<? extends T> iterable): emit된 데이터들이 파라미터로 전달된 iterable의 요소와 매치됨을 기대한다.
 
**시간 기반(Time-based) 테스트**
withVirtualTIme() 메서드는 VirtualTimeScheduler라는 가상 스케줄러의 제어를 받도록 해 줍니다.
VirtualTimeScheduler의 advanceTimeBy()를 이용해 시간을 1시간 당기는 작업을 수행합니다.

expectNoEvent()의 파라미터로 시간을 지정하면 지정한 시간 동안 어떤 이벤트도 발생하지 않을 것이라고 기대하는 동시에 지정한 시간만큼 시간을 앞당긴다.

**BackPressure 테스트**
thenConsumeWhile() 메서드로 emit되는 데이터를 소비할 수 있음.

verifyThenAssertThat() 메서드를 사용하면 검증을 트리거하고 난 후, 추가적인 Assertion을 할 수 있습니다.

hasDroppedElements() 메서드를 이용해서 Drop된 데이ㅓㅌ가 있음을 Assertion합니다.

**Context 테스트**

**Record 기반 테스트**
emit된 데이터의 단순 기댓값만 평가하는 것이 아니라 족므 더 구체적인 조건으로 Assertion해야 하는 경우에 recordWith() 사용.

recordWith()는 파라미터로 전달한 Java의 컬렉션에 emit된 데이터를 추가하는 세션을 시작합니다.

- recordWith(): emit된 데이터에 대한 기록을 시작.
- thenConsumeWhile(): 파라미터로 전달한 Predicate과 일치하는 데이터는 다음 단계에서 소비할 수 있도록 함.
- consumeRecordedWith(): 컬렉션에 기록된 데이터를 소비.

## 13.2 TestPublisher를 사용한 테스팅
TestPublisher: reactor-test 모듈에서 지원하는 테스트 전용 Publisher

**정상 동작하는(Well-behaved) TestPublisher**
TestPublisher를 사용하면 복잡한 로직이 포함된 대상 메서드를 테스트하거나 조건에 따라서 Signal을 변경해야 되는 등의 특정 상황을 테스트하기가 용이할 것입니다.

**오동작하는(Misbeghaving) TestPublisher**
- TestPublisher.createNonCompliant(TestPublisher.Violation.ALLOW_NULL): 
데이터의 값이 null이라도 정상 동작하는 TestPublisher 생성

## 13.3 PublisherProbe를 사용한 테스팅
Sequence의 실행 경로를 테스트할 수 있습니다.

Sequence가 분기되는 상황에서 실제로 어느 Publisher가 동작하는지 해당 Publisher의 실행 경로를 테스트하는 것입니다.