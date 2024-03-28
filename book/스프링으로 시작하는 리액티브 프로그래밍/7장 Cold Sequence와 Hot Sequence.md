## 7.1 Cold와 Hot의 의미
Hot은 무언가 처음부터 다시 시작하지 않고, 같은 작업이 반복되지 않는 느낌이 납니다.  
Cold는 처음부터 새로 시작해야 하고, 새로 시작하기 때문에 같은 작업이 반복될 것입니다.

'Cold는 무언가를 새로 시작하고, Hot은 무언가를 새로 시작하지 않는다'

## 7.2 Cold Sequence
Cold Sequence는 Subscriber가 구독할 때마다 데이터 흐름이 처음부터 다시 시작되는 Sequence 입니다.  

구독 시점이 달라도 구독을 할 때마다 Publisher가 데이터를 emit하는 과정을 처음부터 다시 시작하는 데이터의 흐름을 Cold Sequence라고 부릅니다.
그리고 이 Cold Sequence 흐름으로 동작하는 Publisher를 Cold Publisher라고 합니다.  
-> 월간 잡지를 구독할 때, 구독자가 5월부터 잡지를 구독했는데 1월달 잡지부터 모두 보내줌.

```java
public class Example {
    public static void main(String[] args) {
        Flux<String> coldFlux =
            Flux
                .fromIterable(Arrays.asList("KOREA", "JAPAN", "CHINESE"))
                .map(String::toLowerCase);
        
        coldFlux.subscribe(country -> log.info("# Subscriber1: {}", country));
        
        Thread.sleep(2000L);
        coldFlux.subscribe(country -> log.info("# Subscriber2: {}", country));
    }
}
```

구독이 발생할 때마다 emit된 데이터를 처음부터 다시 전달받음.

## 7.3 Hot Sequence
Hot Sequence는 구독이 발생한 시점 이전에 emit된 데이터를 전달받지 못하고, 구독이 발생한 시점 이후에 emit된 데이터만 전달받을 수 있습니다.

delayElements(): 데이터 소스로 입력된 각 데이터의 emit을 일정시간 동안 지연시키는 Operator   
share(): 원본 Flux 멀티캐스트하는 새로운 Flux를 리턴한다.
여기서 원본 Flux라는 것은 Operator를 통해 가공되지 않은, 즉 원본 데이터 소스를 처음으로 emit하는 Flux를 의미합니다.

fromArray, delayElements, share 모두 Flux를 리턴합니다.
이렇게 Operator 체인 형태로 리턴되는 Flux들은 모두 다른 참조 값을 가지는 객체입니다.
이렇게 리턴되는 Flux 중에서 fromArray()에서 처음으로 리턴하는 Flux가 바로 원본 Flux가 될 것입니다.

이 원본 Flux를 멀티캐스트(또는 공유)한다는 의미는 여러 Subscriber가 하나의 원본 Flux를 공유한다는 의미입니다.

하나의 원본 Flux를 공유해서 다 같이 사용하기 때문에 어떤 Subscriber가 이 원본 Flux를 먼저 구독해버리면 데이터 emit을 시작하게 되고, 
이후에 다른 Subscriber가 구독하는 시점에는 원본 Flux에서 이미 emit된 데이터를 전달받을 수 없게 됩니다.

delayElements()의 디폴트 스레드 스케줄러가 parallel.

## 7.4 HTTP 요청과 응답에서 Cold Sequence와 Hot Sequence의 동작 흐름
cache() Operator를 사용하면 Cold Sequence가 Hot Sequence로 동작하게 됨. 
cache() Operator는 Cold Sequence로 동작하는 Mono를 Hot Sequence로 변경해 주고 emit된 데이터를 캐시한 뒤,
구독이 발생할 때마다 캐시된 데이터를 전달합니다.

결과적으로 캐시된 데이터를 전달하기 떄문에 구독이 발생할 때마다 Subscriber는 동일한 데이터를 전달받게 됩니다.


**Reactor에서 Hot의 두 가지 의미**
- 최초 구독이 발생하기 전까지는 데이터의 emit이 발생하지 않는 것: Warm up
  - share()의 경우 최초 구독이 발생했을 때, 데이터를 emit하는 Warm up의 의미를 가지는 Hot sequence라고 할 수 있습니다.
- 구독 여부와 상관없이 데이터가 emit되는 것: Hot
