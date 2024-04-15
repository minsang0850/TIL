## 14.1 Operator란?
just, create, fromArray, filter, map, 등등의 메서드

## 14.2 Sequence 생성을 위한 Operator
- just: emit할 데이터가 null일 경우 NPE
- justOrEmpty: emit할 데이터가 null일 경우 NPE 발생하지 않고 onComplete Signal 전송
- fromIterable: Iterable에 포함된 데이터를 emit하는 Flux를 생성
- fromStream: Stream에 포함된 데이터를 emit하는 Flux를 생성
- range(n, m): n부터 1씩 증가한 연속된 수를 m개 emit하는 Flux 생성
- defer: 선언한 시점에 데이터를 emit하는 것이 아니라 구독하는 시점에 emit하는 Flux 또는 Mono를 생성. 데이터 emit을 지연시키기 때문에 꼭 필요한 시점에 데이터를 emit하여 불필요한 프로세스를 줄일 수 있음

just() Operator는 Hot Publisher -> Subscriber의 구독 여부와 상관없이 데이터를 emit.
구독이 발생하면 emit된 데이터를 다시 재생(replay)해서 Subscriber에게 전달.

- using: 파라미터로 전달받은 resource를 emit하는 Flux를 생성
- generate: 프로그래밍 방식으로 Signal 이벤트를 발생시키며, 특히 동기적으로 데이터를 하나씩 순차적으로 emit하고자 할 경우 사용
- create: 한 번에 여러 건의 데이터를 비동기적으로 emit할 수 있음. (generate는 데이터를 동기적으로 한 번에 한 건씩 emit)

## 14.3 Sequence 필터링 Operator
- filter: Upstream에서 emit된 데이터 중에서 조건에 일치하는 데이터만 Downstream으로 emit합니다.
- filterWhen: 내부에서 innerSequence를 통해 조건에 맞는 데이터인지를 비동기적으로 테스트한 후, 
테스트 결과가 true라면 filterWhen()의 Upstream으로부터 전달받은 데이터를 Downstream으로 emit
- skip: emit된 데이터 중에서 파라미터로 입력받은 숫자(혹은 시간)만큼 건너뛴 후, 나머지 데이터를 Downstream으로 emit
- take: Upstream에서 emit된 데이터 중에서 파라미터로 입력받은 숫자만큼 건너뛴 후 나머지 데이터를 Downstream으로 emit
- takeLast: Upstream에서 emit된 데이터 주에서 파라미터로 입력한 개수만큼 가장 마지막에 emit된 데이터를 Downstream으로 emit
- takeUntil: 파라미터로 입력한 람다 표현식(Predicate)이 true가 될 때까지 Upstream에서 emit된 데이터를 Downstream으로 emit
  - Upstream에서 emit된 데이터에는 Predicate을 평가할 때 사용한 데이터가 포함된다.
- takeWhile: 파라미터로 입력한 람다 표현식(Predicate)이 true가 되는 동안에만 Upstream에서 emit된 데이터를 Downstream으로 emit
  - Predicate를 평가할 때 사용한 데이터가 Downstream으로 emit 되지 않음.
- next: Upstream에서 emit되는 데이터 중에서 첫 번째 데이터만 Downstream으로 emit. 만일 데이터가 empty라면 Downstream으로 empty Mono를 emit.

## 14.4 Sequence 변환 Operator
- map: emit된 데이터를 mapper Function을 사용하여 변환한 후, Downstream으로 emit
  - 에러 발생 시 Sequence가 종료되지 않고 계속 진행되도록 하는 기능을 지원
- flatMap: Upstream에서 emit된 데이터 한 건이 Inner Sequence에서 여러 건의 데이터로 변환되고
  - Upstream에서 emit된 데이터는 Inner Sequence에서 평탄화 작업을 거치면서 하나의 Sequence로 병합
- concat: 파라미터로 입력되는 Publisher의 Sequence를 연결해서 데이터를 순차적으로 emit
- merge: 파라미터로 입력되는 Publisher의 Sequence에서 emit된 데이터를 인터리빙(Interleave) 방식으로 병합
- zip: 파라미터로 입력되는 Publisher에서 emit된 데이터를 결합하는데, 각 Publisher가 데이터를 하나씩 emit할 때까지 기다렸다가 결합
- and: Mono의 Complete Signal과 파라미터로 입력된 Publisher의 Complete Signal을 결합하여 새로운 Mono<Void>를 반환
- collectList: emit된 데이터를 모아서 List로 변환한 후 emit하는 Mono를 반환
- collectMap: emit된 데이터를 기반으로 key와 value를 생성하여 Map의 Element로 추가한 후, 최종적으로 Map을 emit하는 Mono를 반환

## 14.5 Sequence의 내부 동작 확인을 위한 Operator
doOnXXXX(): Consumer 또는 Runnable 타입의 함수형 인터페이스를 파라미터로 가짐 -> 별도의 리턴 값 없음

## 14.6 에러 처리를 위한 Operator
- error: 파라미터로 지정된 에러로 종료하는 Flux를 생성
- onErrorReturn: 에러 이벤트가 발생했을 때, 에러 이벤트를 Downstream으로 전파하지 않고 대체 값을 emit
- onErrorResume: 에러 이벤트가 발생했을 때, 에러 이벤트를 Downstream으로 전파하지 않고 대체 Publisher를 리턴
- onErrorContinue: 에러 이벤트가 발생했을 때, 에러 영역 내에 있는 데이터는 제거하고, Upstream에서 후속 데이터를 emit하는 방식으로 에러를 복구할 수 있도록 해 줍니다.
- retry: Publisher가 데이터를 emit하는 과정에서 에러가 발생하면 파라미터로 입력한 횟수만큼 원본 Flux의 Sequence를 다시 구독

## 14.7 Sequence의 동작 시간 측정을 위한 Operator
- elapsed: emit된 데이터 사이의 경과 시간을 측정해서 Tuple<Long, T> 형태로 Downstream에 emit

## 14.8 Flux Sequence 분할을 위한 Operator
- window: Upstream에서 emit되는 첫 번째 데이터부터 maxSize 숫자만큼의 데이터를 포함하는 새로운 Flux로 분할합니다.
- buffer: Upstream에서 emit되는 첫 번째 데이터부터 maxSize 숫자만큼의 데이터를 List 버퍼로 한 번에 emit합니다.
- bufferTimeout: Upstream에서 emit되는 첫 번째 데이터부터 maxSize 숫자만큼의 데이터 또는 maxTime 내에 emit된 데이터를 List 버퍼로 한 번에 emit합니다.
- groupBy: emit되는 데이터를 keyMapper로 생성한 key를 기준으로 그룹화한 GroupedFlux를 리턴하며, 이 GroupedFlux를 통해서 그룹별로 작업을 수행

## 14.9 다수의 Subscriber에게 Flux를 멀티캐스팅(Multicasting)하기 위한 Operator
- publish: 구독을 하더라도 구독 시점에 즉시 데이터를 emit하지 않고, connect()를 호출하는 시점에 비로소 데이터를 emit
- autoConnect: 파라미터로 지정하는 숫자만큼의 구독이 발생하는 시점에 Upstream 소스에 자동으로 연결되기 때문에 별도의 connect 호출이 필요하지 않습니다.
- refCount: 파라미터로 입력된 숫자만큼의 구독이 발생하는 시점에 Upstream 소스에 연결되며, 모든 구독이 취소되거나 Upstream의 데이터 emit이 종료되면 연결이 해제됩니다.