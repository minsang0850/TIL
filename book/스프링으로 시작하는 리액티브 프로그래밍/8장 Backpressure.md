## 8.1 BackPressure란?
리액티브 프로그래밍에서의 배압, 즉 BackPressure는 콘서트장에서 관객의 입장을 통제하는 안전요원에 비유할 수 있습니다.
Publisher가 끊임없이 emit하는 무수히 많은 데이터를 적절하게 제어하여 데이터 처리에 과부하가 걸리지 않도록 제어하는 것이 바로 BackPressure의 역할인 것입니다.

emit된 데이터들이 처리되지 못하고 기다리는데 Publisher는 아주 빠른 속도로 데이터를 끊임없이 emit하면 어떻게 될까요?
처리되지 않고 대기 중인 데이터가 지속적으로 쌓이게 되면 오버플로가 발생하거나 최악의 경우에는 시스템이 다운되는 문제가 발생하게 됩ㄴ디ㅏ.

## 8.2 Reactor에서의 Backpressure 처리 방식
### 8.2.1 데이터 개수 제어
첫 번째 방식은 Subscriber가 적절히 처리할 수 있는 수준의 데이터 개수를 Publisher에게 요청하는 것입니다.

### 8.2.2 Backpressure 전략 사용
Reactor에서 제공하는 Backpressure 전략을 사용하는 것입니다.

**Backpressure 전략**
- IGNORE: Backpressure를 적용하지 않는다.
- ERROR: Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, Exception을 발생시키는 전략
- DROP: Downsteram으로 전달할 데이터가 버퍼에 가득 찰 경우, 버퍼 밖에서 대기하는 먼저 emit된 데이터부터 Drop 시키는 전략
- LATEST: Downsteram으로 전달할 데이터가 버퍼에 가득 찰 경우, 버퍼 밖에서 대기하는 가장 최근에(나중에) emit된 데이터부터 버퍼에 채우는 전략
- BUFFER: Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, 버퍼 안에 있는 데이터부터 Drop시키는 전략

**ERROR 전략**
Downstream의 데이터 처리 속도가 느려서 Upstream의 emit 속도를 따라가지 못할 경우 IllegalStateException을 발생시킵니다.
이 경우 Publisher는 Error Signal을 Subscriber에게 전송하고 삭제한 데이터는 폐기합니다.

```java
public class Example {
    public static void main(String[] args) {
        Flux
            .interval(Duration.ofMillis(1L)) // 0 부터 1 씩 증가한 숫자를 1ms에 한번 씩 emit
            .onBackpressureError() // Error 전략
            .doOnNext(data -> log.info("# doOnNext: {}", data))
            .publishOn(Schedules.parallel())  // Reactor Sequence 중 일부를 별도의 스레드에서 실행할 수 있도록 해 주는 Operator, 별도의 스레드가 하나 더 실행된다고 생각.
            .subscribe(data -> {
                try {
                    Thread.sleep(5L); // 데이터 처리에 5ms
                } catch (InterruptedException e) {
                }
                log.info("# onNext: {}", data);
            },
                       error -> log.error("# onError"));
            Thread.sleep(2000L);
    }
}
```
onNext 로그에서 255를출력하고 OverflowException이 발생하면서 Sequence가 종료.

**DROP 전략**
Publisher가 Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, 버퍼 밖에서 대기 중인 데이터 중에서 먼저 emit된 데이터부터 Drop 시키는 전략입니다.
Drop된 데이터는 폐기됩니다.

```java
public class Example {
    public static void main(String[] args) {
        Flux
            .interval(Duration.ofMillis(1L)) // 0 부터 1 씩 증가한 숫자를 1ms에 한번 씩 emit
            .onBackpressureDrop(dropped -> log.info("# dropped: {}", dropped)) // Drop 전략
            .publishOn(Schedules.parallel())  // Reactor Sequence 중 일부를 별도의 스레드에서 실행할 수 있도록 해 주는 Operator, 별도의 스레드가 하나 더 실행된다고 생각.
            .subscribe(data -> {
                           try {
                               Thread.sleep(5L); // 데이터 처리에 5ms
                           } catch (InterruptedException e) {
                           }
                           log.info("# onNext: {}", data);
                       },
                       error -> log.error("# onError", error));
        Thread.sleep(2000L);
    }
}
```
onBackpressureError() operator와 달리 onBackpressureDrop() Operator는 Drop된 데이터를 파라미터로 전달받을 수 있기 떄문에 Drop된 데이터가 폐기되기 전에 추가 작업을 수행할 수 있습니다.

DROP 전략에서는 버퍼가 가득 찬 상태에서는 버퍼가 비워질 때까지 데이터를 Drop 합니다.

**LATEST 전략**
Publisher가 Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, 버퍼 밖에서 대기 중인 데이터 중에서 가장 최근에(나중에) emit된 데이터부터 버퍼에 채우는 전략입니다.

LATEST 전략은 새로운 데이터가 들어오는 시점에 가장 최근에 데이터만 남겨 두고 나머지 데이터를 폐기합니다. 
```java
public class Example {
    public static void main(String[] args) {
        Flux
            .interval(Duration.ofMillis(1L)) // 0 부터 1 씩 증가한 숫자를 1ms에 한번 씩 emit
            .onBackpressureLatest()
            .publishOn(Schedules.parallel())  // Reactor Sequence 중 일부를 별도의 스레드에서 실행할 수 있도록 해 주는 Operator, 별도의 스레드가 하나 더 실행된다고 생각.
            .subscribe(data -> {
                           try {
                               Thread.sleep(5L); // 데이터 처리에 5ms
                           } catch (InterruptedException e) {
                           }
                           log.info("# onNext: {}", data);
                       },
                       error -> log.error("# onError", error));
        Thread.sleep(2000L);
    }
}
```
255 출력 이후에 숫자 1037을 출력
-> 버퍼가 가득 찼다가 버퍼가 다시 비워지는 시간 동안 emit되는 데이터가 가장 최근에 emit된 데이터가 된 후, 다음 데이터가 emit되면 다시 폐기되는 이 과정을 반복하기 때문입니다.

**BUFFER 전략**
버퍼의 데이터를 폐기하지 않고 버퍼링을 하는 전략을 지원하지만, 버퍼가 가득 차면 버퍼 내의 데이터를 폐기하는 전략, 그리고 버퍼가 가득 차면 에러를 발생시키는 전략도 지원합니다.

DROP, LATEST 전략은 버퍼가 가득 찼을 떄 버퍼가 지워질 떄까지 버퍼 바깥쪽에 있는 데이터를 폐기하는 방식.  
BUFFER 전략에서의 데이터 폐기는 BUFFER가 가득 찼을 때 BUFFER 바깥쪽이 아닌 버퍼 안에 있는 데이터를 폐기하는 것을 의미합니다.

BUFFER 전략 중에서 데이터를 폐기하는 전략에는 DROP_LATEST 전략과 DROP_OLDEST 전략 이렇게 두 가지가 있습니다.

**DROP_LATEST 전략**
Publisher가 Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, 가장 최근에 버퍼 안에 채워진 데이터를 Drop하여 폐기한 후, 이렇게 확보된 공간에 emit된 데이터를 채우는 전략입니다.

```java
public class Example8_5 {
    public static void main(String[] args) throws InterruptedException {
        Flux
            .interval(Duration.ofMillis(300L))
            .doOnNext(data -> log.info("# emitted by original Flux: {}", data))
            .onBackpressureBuffer(2,   // 1번 파라미터: 버퍼의 최대 용량
                    dropped -> log.info("** Overflow & Dropped: {} **", dropped), // 2번 파라미터: 버퍼 오버플로가 발생했을 떄, Drop되는 데이터를 전달받아 후처리를 할 수 있습니다.
                    BufferOverflowStrategy.DROP_LATEST)                 // 3번 파라미터: 적용할 Backpressure 전략
            .doOnNext(data -> log.info("[ # emitted by Buffer: {} ]", data))
            .publishOn(Schedulers.parallel(), false, 1)     // prefetch 수를 1로 지정
            .subscribe(data -> {
                        try {
                            Thread.sleep(1000L);
                        } catch (InterruptedException e) {}
                        log.info("# onNext: {}", data);
                    },
                    error -> log.error("# onError", error));

        Thread.sleep(2500L);
    }
}
```

**BUFFER DROP_OLDEST 전략**
Publisher가 Downstream으로 전달할 데이터가 버퍼에 가득 찰 경우, 버퍼 안에 채워진 데이터 중에서 가장 오래된 데이터를 Drop하여 폐기한 후, 확보된 공간에 emit된 데이터를 채우는 전략입니다.

```java
public class Example8_6 {
    public static void main(String[] args) throws InterruptedException {
        Flux
            .interval(Duration.ofMillis(300L))
            .doOnNext(data -> log.info("# emitted by original Flux: {}", data))
            .onBackpressureBuffer(2,  // 1번 파라미터: 버퍼의 최대 용량
                    dropped -> log.info("** Overflow & Dropped: {} **", dropped),
                    BufferOverflowStrategy.DROP_OLDEST)
            .doOnNext(data -> log.info("[ # emitted by Buffer: {} ]", data))
            .publishOn(Schedulers.parallel(), false, 1)
            .subscribe(data -> {
                        try {
                            Thread.sleep(1000L);
                        } catch (InterruptedException e) {}
                        log.info("# onNext: {}", data);
                    },
                    error -> log.error("# onError", error));

        Thread.sleep(2500L);
    }
}
```