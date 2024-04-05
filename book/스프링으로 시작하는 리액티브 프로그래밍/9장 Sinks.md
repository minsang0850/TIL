## 9.1 Sinks란?
Sinks란 무엇인가?  
Sink는 리액티브 스트림즈의 구성요소 중 하나로 언급했던 Processor는 Publisher와 Subscriber의 기능을 모두 지니기 때문에 Subscriber로서 기능할 땐 다른 Publisher를 구독할 수 있고,
Publisher로서 기능할 땐 다른 Subscriber가 구독할 수 있습니다.

Reactor에서는 Processor 인터페이스를 구현한 구현 클래스인 FluxProcessor, MonoProcessor, EmitterProcessor 등을 지원합니다.

Processor의 기능을 개선한 Sinks가 Reactor 3.4.0 버전부터 지원되기 시작했으며, 
Processor와 관련된 API는 REactor 3.5.0 부터 완전히 제거될 예정입니다.

---
**Reactor API 문서에서의 Sinks**  
'Sinks는 리액티브 스트림즈의 Signal을 프로그래밍 방식으로 푸시할 수 있는 구조이며 Flux 또는 Mono의 의미 체계를 가진다.'  
지금까지 배운 방식은 모두 Flux 또는 Mono가 onNext 같은 Signal을 내부적으로 전송해 주는 방식이었는데, Sinks를 사용하면 프로그래밍 코드를 통해 명시적으로 Signal을 전송할 수 있습니다.

Reactor에서 프로그래밍 방식으로 Signal을 전송하는 가장 일반적인 방법은 generate() Operator나 create() Operator 등을 사용하는 것인데,
이는 Reactor에서 Sinks를 지원하기 전부터 이미 사용하던 방식입니다.

Sinks는 멀티스레드 방식으로 Signal을 전송해도 스레드 안정성을 보장하기 때문에 예기치 않는 동작으로 이어지는 것을 방지해 줍니다.

---

```java
public class Example9_1 {
    public static void main(String[] args) throws InterruptedException {
        int tasks = 6;
        Flux
            .create((FluxSink<String> sink) -> {   // 1번 Operator가 처리해야 할 작업의 개수만큼 doTask() 메서드를 호출해서 작업을 처리한 후, 결과를 리턴받습니다.
                IntStream
                        .range(1, tasks)
                        .forEach(n -> sink.next(doTask(n)));
            })
            .subscribeOn(Schedulers.boundedElastic())  // 3번 작업을 처리하는 스레드.
            .doOnNext(n -> log.info("# create(): {}", n))  
            .publishOn(Schedulers.parallel())   // 4번 subscribeOn에서 처리된 결과를 가공.
            .map(result -> result + " success!")   // 2번 처리 결과를 추가적으로 가공 처리, 최종적으로 Subscriber에게 전달.
            .doOnNext(n -> log.info("# map(): {}", n))   
            .publishOn(Schedulers.parallel())       // 5번 가공된 결과를 Subscriber에게 전달하는 스레드
            .subscribe(data -> log.info("# onNext: {}", data));  

        Thread.sleep(500L);
    }

    private static String doTask(int taskNumber) {
        // now tasking.
        // complete to task.
        return "task " + taskNumber + " result";
    }
}
```
총 3개의 스레드가 동시에 실행됩니다.

- doTask()의 작업 처리는 boundedElastic 스레드에서 실행.
- map() Operator에서의 가공 처리는 parallel-2 스레드에서 실행
- Subscriber에서 전달받은 데이터의 처리는 parallel-1 스레드에서 실행

create 오퍼레이터를 사용해서 프로그래밍 방식으로 Signal을 전송할 수 있으며,
Reactor Sequence를 단계적으로 나누어서 여러 개의 스레드로 처리할 수 있습니다.

위 코드에서 작업을 처리한 후, 
그 결과 값을 반환하는 doTask() 메서드가 싱글스레드가 아닌 여러 개의 스레드에서 각각의 전혀 다른 작업들을 처리한 후, 
처리 결과를 반환하는 상황이 발생할 수도 있습니다.
이 같은 상황에서 적절하게 사용할 수 있는 방식이 Sinks 입니다.
---
```java
public class Example9_2 {
    public static void main(String[] args) throws InterruptedException {
        int tasks = 6;

        Sinks.Many<String> unicastSink = Sinks.many().unicast().onBackpressureBuffer();
        Flux<String> fluxView = unicastSink.asFlux();
        IntStream
                .range(1, tasks)
                .forEach(n -> {
                    try {
                        new Thread(() -> {
                            unicastSink.emitNext(doTask(n), Sinks.EmitFailureHandler.FAIL_FAST);
                            log.info("# emitted: {}", n);
                        }).start();
                        Thread.sleep(100L);
                    } catch (InterruptedException e) {
                        log.error(e.getMessage());
                    }
                });

        fluxView
                .publishOn(Schedulers.parallel())
                .map(result -> result + " success!")
                .doOnNext(n -> log.info("# map(): {}", n))
                .publishOn(Schedulers.parallel())
                .subscribe(data -> log.info("# onNext: {}", data));

        Thread.sleep(200L);
    }

    private static String doTask(int taskNumber) {
        // now tasking.
        // complete to task.
        return "task " + taskNumber + " result";
    }
}
```
9-1과 달리 doTask() 메서드가 루프를 돌 때마다 새로운 메서드에서 실행됩니다.
doTask() 메서드의 작업 처리 결과를 Sinks를 통해서 Downstream에 emit합니다.

doTask() 메서드는 루프를 돌 때마다 새로운 스레드에서 실행되기 때문에 총 5개의 스레드에서 실행되고,
map() Operator에서의 가공 처리는 parallel-2 스레드, 
Subscriber에서 전달받은 데이터의 처리는 parallel-1 스레드에서 실행되어 코드 9-2는 총 7개의 스레드가 실행되었습니다.

Sinks는 프로그래밍 방식으로 Singal을 전송할 수 있으며, 멀티스레딩 환경에서 스레드 안전성을 보장받을 수 있는 장점이 있습니다.

## 9.2 Sinks 종류 및 특징
Sinal을 전송하는 방법은 크게 두가지.
1. Sinks.One
2. Sinks.Many

### Sinks.One
```java
public final class Sinks {
    public static <T> Sinks.One<T> one() {
        return SinksSpecs.DEFAULIT_ROIOT_SPEC.one();
    }
}
```
Sinks.One은 한 건의 데이터를 프로그래밍 방식으로 emit 하는 역할을 하기도 하고,
Mono 방식으로 Subscriber가 데이터를 소비할 수 있도록 해 주는 Sinks 클래스 내부에 인터페이스로 정의된 Sinks의 스펙 또는 사양으로 볼 수 있습니다.
```java
// emit 된 데이터 중에서 단 하나의 데이터만 Subscriber에게 전달한다. 나머지 데이터는 Drop 됨.
@Slf4j
public class Example9_4 {
    public static void main(String[] args) throws InterruptedException {
        Sinks.One<String> sinkOne = Sinks.one();
        Mono<String> mono = sinkOne.asMono();

        sinkOne.emitValue("Hello Reactor", FAIL_FAST);
        sinkOne.emitValue("Hi Reactor", FAIL_FAST);
        sinkOne.emitValue(null, FAIL_FAST);

        mono.subscribe(data -> log.info("# Subscriber1 {}", data));
        mono.subscribe(data -> log.info("# Subscriber2 {}", data));
    }
}
```

```java
public final class Sinks {
    public interface EmitFailureHandler {
        EmitFailureHandler FAIL_FAST = (signalType, emission) -> false;
        boolean onEmitFailure(SignalType signalType, EmitResult emitResult);
    }
}
```
에러가 발생했을 때 재시도를 하지 않고 즉시 실패 처리를 한다.

asMono() 메서드로 Mono 객체로 변환.
Mono 객체를 통해 emit된 데이터를 받을 수 있다.

Sinks.One으로 아무리 많은 수의 데이터를 emit한다 하더라도 처음 emit한 데이터는 정상적으로 emit되지만 나머지 데이터들은 Drop 된다는 사실을 알 수 있습니다.

### Sinks.Many
Sinks.many()의 경우 Sinks.Many를 리턴하지 않고 ManySpec 이라는 인터페이스를 리턴합니다.

Sinks.One의 경우 단순히 한 건의 데이터를 emit하는 한 가지 기능만 가지기 때문에 별도의 Spec이 정의되는 게 아니라 Default spec을 사용.  
Sinks.Many의 경우, 데아터 emiot을 위한 여러 가지 기능이 정의된 ManySpec을 리턴
```java
public final class Sinks {
    public interface ManySpec {
        UnicastSpec unicast();
        MulticastSpec multicast();
        MulticastReplaySpec replay();
    }
}
```
```java
// unicast()통해 단 하나의 Subscriber만 데이터를 전달 받을 수 있다
@Slf4j
public class Example9_8 {
    public static void main(String[] args) throws InterruptedException {
        Sinks.Many<Integer> unicastSink = Sinks.many().unicast().onBackpressureBuffer();
        Flux<Integer> fluxView = unicastSink.asFlux();

        unicastSink.emitNext(1, FAIL_FAST);
        unicastSink.emitNext(2, FAIL_FAST);


        fluxView.subscribe(data -> log.info("# Subscriber1: {}", data));

        unicastSink.emitNext(3, FAIL_FAST);

        fluxView.subscribe(data -> log.info("# Subscriber2: {}", data));
    }
}
```
unicastSpec에 정의된 기능은 onBackpressureBuffer() 메서드를 호출함으로써 사용하게 됩니다.

UnicastSpec의 기능이 단 하나의 Subscriber에게만 데이터를 emit하는 것이기 때문에 아마도 두 번째 Subscriber에게 전달되지 않든가 에러가 발생하든가 둘 중 하나일 것입니다.

UnicastProcessor는 하나의 Subscriber만 허용.

```java
// replay()를 사용하여 이미 emit된 데이터 중에서 특정 개수의 최신 데이터만 전달하는 예제
@Slf4j
public class Example9_10 {
    public static void main(String[] args) {
        Sinks.Many<Integer> replaySink = Sinks.many().replay().limit(2);
        Flux<Integer> fluxView = replaySink.asFlux();

        replaySink.emitNext(1, FAIL_FAST);
        replaySink.emitNext(2, FAIL_FAST);
        replaySink.emitNext(3, FAIL_FAST);

        fluxView.subscribe(data -> log.info("# Subscriber1: {}", data));

        replaySink.emitNext(4, FAIL_FAST);

        fluxView.subscribe(data -> log.info("# Subscriber2: {}", data));
    }
}
```
replay() 메서드를 호출하면 리턴 값으로 MulticastReplaySpec을 리턴하고,
이 MulticastReplaySpec의 구현 메서드 중 하나인 limit() 메서드를 호출합니다.

MulticastReplaySpec에는 emit된 데이터를 다시 replay 해서 구독 전에 이미 emit된 데이터라도 Subscriber가 전달받을 수 있게 하는 다양한 메서드들이 정의돼 있습니다.

**all()**  
구독 전에 emit된 데이터가 있더라도 처음 emit된 데이터부터 모든 데이터들이 Subscriber에게 전달.

limit(n) 메서드는 가장 나중에 emit된 데이터부터 Subscriber에게 전달. 
emit된 데이터 중에서 n개만 뒤로 돌려서 전달하겠다는 의미입니다.
