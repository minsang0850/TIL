## 15.1 Spring WebFlux의 탄생 배경
Spring WebFlux는 리액티브 웹 애플리케이션 구현을 위해 Spring 5.0부터 지원하는 리액티브 웹 프레임워크입니다.

## 15.2 Spring WebFlux의 기술 스택
### 서버
Spring WebFlux는 Non-Blocking I/O 방식으로 동작하는 Netty 등의 서버 엔진에서 동작

### 서버 API
Spring WebFlux는 기본 서버 엔진이 Netty이지만 Jetty나 Undertow 같은 서버 엔진에서 지원하는 리액티브 스트림즈 어댑터를 통해 리액티브 스트림즈를 지원합니다.

### 보안
WebFilter를 이용해 Spring Security를 사용

### 데이터 액세스
데이터 액세스 계층까지 완벽하게 Non-Blocking I/O를 지원할 수 있도록
Spring Data R2DBC 및 Non-Blocking I/O를 지원하는 NoSQL 모듈을 사용합니다.

### 15.3 Spring WebFlux의 요청 처리 흐름
1. 최초 클라이언트 요청이 들어오면 Netty 등의 서버 엔진을 거쳐 HttpHandler가 들어오는 요청을 전달받습니다.
   1. ServerHttpRequest, ServerHttpResponse를 포함하는 ServerWebExchange를 생성한 후, WebFilter 체인으로 전달
2. ServerWebExchange는 WebFilter 체인에서 전처리 과정을 거친 후, WebHandler의 구현체인 DispatcherHandler에게 전달됩니다.
3. DispatcherHandler에서는 HandlerMapping List를 원본 Flux의 소스로 전달받습니다.
4. ServerWebExchange를 처리할 핸들러를 조회합니다.
5. 조회한 핸들러의 호출을 HandlerAdapter에게 위임합니다.
6. HandlerAdapter는 ServerWebExchange를 처리할 핸들러를 호출합니다.
7. Controller 또는 HandlerFunction 형태의 핸들러에서 요청을 처리한 후, 응답 데이터를 리턴합니다.
8. 핸들러로부터 리턴받은 응답 데이터를 처리할 HandlerResultHandler를 조회합니다.
9. 조회한 HandlerResultHandler가 응답 데이터를 적절하게 처리한 후, response로 리턴합니다.

### 15.4 Spring WebFlux 핵심 컴포넌트
### HttpHandler
request와 response를 처리하기 위해 추상화된 단 하나의 메서드(handle)만 가집니다.

### WebFilter
핸들러가 요청을 처리하기 전에 전처리 작업을 할 수 있도록 해 줍니다.

보안이나 세션 타임아웃 처리 등 애플리케이션에서 필요한 전처리에 사용됩니다.

### HandlerFilterFunction
함수형 기반의 요청 핸들러에 적용할 수 있는 Filter

WebFilter의 구현체는 Spring Bean으로 등록되는 반면, HandlerFilterFunction 구현체는 애너테이션 기반의 핸들러가 아닌 함수형 기반의 요청 핸들러에서 함수 형태로 사용되기 때문에 Spring Bean으로 등록되지 않는다는 차리점이 있습니다.

### DispatcherHandler
WebHandler의 구현체로서 DispatcherServlet처럼 중앙에서 먼저 요청을 전달받은 후에 다른 컴포넌트에 요청 처리를 위임합니다.

### HandlerMapping
Spring MVC에서와 마찬가지로 request와 handler object에 대한 매핑을 정의하는 인터페이스.

### HandlerAdapter
HandlerMapping을 통해 얻은 핸들러를 직접적으로 호출하는 역할을 하며, 응답 결과로 Mono<HandlerResult>를 리턴받습니다.

## 15.5 Spring WebFlux의 Non-Blocking 프로세스 구조
스레드가 차단되지 않기 때문에 적은 수의 고정된 스레드 풀을 사용해서 더 많은 요청을 처리합니다.

Spring WebFlux가 스레드 차단 없이 더 많은 요청을 처리할 수 있는 이유는 요청 처리 방식으로 이벤트 루프 방식을 사용하기 때문입니다.

**프로세스**
1. 클라이언트로부터 들어오는 요청을 요청 핸들러가 전달받습니다.
2. 전달받은 요청을 이벤트 루프에 푸시합니다.
3. 이벤트 루프는 네트워크, DB 연결 작업 등 비용이 드는 작업에 대한 콜백을 등록합니다.
4. 작업이 완료되면 완료 이벤트를 이벤트 루프에 푸시합니다.
5. 등록한 콜백을 호출해 처리 결과를 전달합니다.

## 15.6 Spring WebFlux의 스레드 모델
Reactor Netty에서는 CPU 코어의 개수가 4보다 적은 경우 최소 4개의 워커 스레드를 생성하고, 4보다 더 많다면 코어 개수만큼의 스레드를 생성합니다.

Spring WebFlux는 Reactor Netty 같은 서버 엔진에서 지원하는 워커 스레드 풀을 통해 적은 수의 스레드로 대량의 요청을 효과적으로 처리할 수 있습니다.
그런데 서버 측에서 복잡한 연산을 처리하는 등의 CPU 집약적인 작업을 하거나, 클라이언트의 요청부터 응답 처리 전 과정 안에 Blocking 되는 지점이 존재한다면 오히려 성능이 저하될 수 있습니다.

성능 저하를 보완하고자 클라이언트의 요청을 처리하기 위해 서버 엔진에서 제공하는 스레드 풀이 아닌 다른 스레드 풀을 사용할 수 있는 메커니즘을 제공하는데, 그것은 바로 Scheduler입니다. 