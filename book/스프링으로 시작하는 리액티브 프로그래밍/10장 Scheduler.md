## 10.1 스레드(Thread)의 개념 이해
Reactor에서 사용되는 Scheduler는 Reactor Sequence에서 사용되는 스레드를 관리해 주는 관리자 역할을 합니다.

- 물리적 스레드: 하드웨어의 스레드, 병렬성과 관련
- 논리적 스레드: 소프트웨어적으로 생성되는 스레드, 동시성과 관련

## 10.2 Scheduler란?
Reactor에서의 Scheduler는 운영체제에서 사용되는 Scheduler의 의미와 비슷하다고 볼 수 있습니다.

- OS Scheduler: 프로세스를 선택하고 실행하는 등 프로세스의 라이프 사이클을 관리
- Reactor Scheduler: 비동기 프로그래밍을 위해 사용되는 스레드를 관리
  - 스레드의 제어를 대신해 주기 때문에 개발자가 직접 스레드를 제어해야 하는 부담에서 벗어날 수 있음

## 10.3 Scheduler를 위한 전용 Operator
Reactor에서 Scheduler는 Scheduler 전용 Operator를 통해 사용할 수 있습니다.

### subscribeOn()
구독이 발생한 직후 실행될 스레드를 지정하는 Operator.

구독이 발생하면 원본 Publisher가 데이터를 최초로 emit하게 되는데, 
subscribeOn() Operator는 구독 시점 직후에 실행되기 때문에 원본 Publisher의 동작을 수행하기 위한 스레드라고 볼 수 있습니다.

### publishOn()
Downstream으로 Signal을 전송할 때 실행되는 스레드를 제어하는 역할을 하는 Operator.

### parallel()
라운드 로빈 방식으로 CPU 코어 개수(물리적인 스레드 개수)만큼의 스레드를 병렬로 실행.

## 10.4 publishOn()과 subscribeOn()의 동작 이해
Operator 체인상에서 한 개 이상의 publishOn() Operator를 사용하여 실행 스레드를 목적에 맞게 적절하게 분리할 수 있다.

subscribeOn() 과 publishOn() Operator를 함께 사용하면 원본 Publisher에서 데이터를 emit하는 스레드와 emit된 데이터를 가공 처리하는 스레드를 적절하게 분리할 수 있다.

## 10.5 Scheduler의 종류
### Schedulers.immediate()
별도의 스레드를 추가적으로 생성하지 않고, 현재 스레드에서 작업을 처리하고자 할 때 사용.

Scheduler가 필요한 API가 있긴 한데 별도의 스레드를 추가 할당하고 싶지 않을 경우에 Schedulers.immediate()을 사용. -> 무슨 말..?

### Schedulers.single()
스레드 하나만 생성해서 Scheduler가 제거되기 전까지 재사용하는 방식.

하나의 스레드로 다수의 작업을 처리해야 되므로 지연시간이 짧은 작업을 처리하는 것이 효과적.

### Schedulers.newSingle()
호출할 때마다 매번 새로운 스레드 하나를 생성.

### Schedulers.boundedElastic()
ExecutorService 기반의 스레드 풀을 생성한 후, 그 안에서 정해진 수만큼의 스레드를 사용하여 작업을 처리하고 작업이 종료된 스레드는 반납하여 재사용하는 방식입니다.

Blocking I/O에 효과적.

### Schedulers.parallel()
Non-Blocking I/O에 최적화.
CPU 코어 수 만큼의 스레드를 생성.

### Schedulers.fromExecutorService
ExecutorSrevice로부터 Scheduler를 생성하는 방식.

Reactor에서 권장되는 방식은 아님.

### Schedulers.newXXXX()
새로운 Scheduler 인스턴스를 생성.

커스텀 스레드 풀을 새로 생성할 수 있음.