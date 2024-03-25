## 5.1 Reactor란?
Reactor는 Spring Framework 팀의 주도하에 개발된 리액티브 스트림즈의 구현체로써 Spring Framework 5 버전부터 리액티브 스택에 포함되어 Spring WebFlux 기반의 리액티브 애플리케이션을 제작하기 위한 핵심 역할을 담당합니다.

리액티브 스트림즈의 구현체인 Reactor는 리액티브 프로그래밍을 위한 라이브러리라고 정의할 수 있습니다.

Reactor의 공식 사이트: https://projectreactor.io

**특징**
1. Reactive Stremas: Reactor는 리액티브 스트림즈 사양을 구현한 리액티브 라이브러리입니다. 
리액티브 스트림즈는 반복해서 설명했기 때문에 이제는 익숙한 용어가 되셨을 거라고 생각합니다.
2. Non-Blocking: Reactor는 JCM 위에서 실행되는 Non-Blocking 애플리케이션을 제작하기 위해 필요한 핵심 기술입니다.
3. Java's functional API: Reactor에서 Publisher와 Subscriber 간의 상호작용은 Java의 함수형 프로그래밍 API를 통해서 이루어집니다.
4. Flux[N]: N개의 데이터를 emit 한다는 의미. Flux는 0개부터 N개, 즉 무한대의 데이터를 emit할 수 있는 Reactor의 Publisher입니다.
5. Mono[0|1]: Mono 역시 Reactor에서 지원하는 Publisher 타입인데, 이와 같이 표현된 이유는 Mono가 데이터를 한 건도 emit하지 않거나 단 한 건만 emit하는 단발성 데이터 emit에 특화된 Publisher이기 때문입니다.
6. Well-suited for microservices
7. Backpressure-ready network:Reactor는 Publisher로부터 전달받은 데이터를 처리하는 데 있어 과부하가 걸리지 않도록 제어하는 BackPressure를 지원합니다.

## 5.2 Hello Reactor 코드로 보는 Reactor의 구성요소
```java
public class Example5_1 {
    public static void main(String[] args) {
        Flux<String> sequence = Flux.just("Hello", "Reactor");
        sequence.map(data -> data.toLowerCase())
            .subscribe(data -> System.out.println(data));
    }
}
```
Flux는 Reactor에서 Publisher의 역할을 합니다.

just 메서드의 파라미터로 전달한 "Hello", "Reactor"가 바로 입력으로 들어오는 데이터가 됩니다.
이 데이터는 Publisher가 최초로 제공하는 가공되지 않은 데이터로서 데이터 소스라고 불립니다.

이 데이터 소스의 데이터 개수가 둘이기 때문에 N건의 데이터를 처리할 수 있는 Reactor의 Publisher 타입인 Flux를 사용했습니다.

data -> System.out.println(data)가 바로 Subscriber의 역할을 합니다.

이 람다 표현식은 Consumer 함수형 인터페이스이며, 내부적으로는 LambdaSubscriber라는 클래스에 전달되어 데이터를 처리하는 역할을 하게 됩니다.

just(), map()은 Reactor에서 지원하는 Operator 메서드인데, just() Operator는 데이터를 생성해서 제공하는 역할을 하고, map() Operator는 전달받은 데이터를 가공하는 역할을 합니다.
just() Operator의 리턴 값이 Flux임을 확인할 수 있습니다. 이를 통해 Reactor의 Operator는 리턴 값으로 Flux를 반환하기 때문에 Operator 체인을 형성하여 또 다른 Operator를 연속적으로 호출할 수 있다는 사실을 알 수 있습니다.

위 코드를 Reactor의 핵심 구성요소를 대부분 포함합니다.
'데이터를 생성해서 제공하고(1단계), 데이터를 가공한 후에(2단계), 전달받은 데이터를 처리한다(3단계)'는 이 세 가지 단계는 데이터를 가공 처리하는 단계가 얼마나 복잡해지느냐, 
스레드를 어떤 식으로 제어하느냐 등에 추가 작업과 상관없이 수행되는 필수 단계라는 사실을 기억하기 바랍니다.