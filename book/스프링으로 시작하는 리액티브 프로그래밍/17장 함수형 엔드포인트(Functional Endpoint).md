## 17.1 HandlerFunction을 사용한 request 처리
HandlerFunction은 요청 처리를 위한 ServerRequest 하나만 handler() 메서드의 파라미터로 전달받으며, 요청 처리에 대한 응답은 Mono<ServerResponse>의 형태로 리턴됩니다.

### ServerRequest
HandlerFunction에 의해 처리되는 HTTP request를 표현합니다.

### ServerResponse
HandlerFunction 또는 HandlerFilterFunction에서 리턴되는 HTTP response를 표현합니다.

## 17.2 request 라우팅을 위한 RouterFunction
RounterFunction은 들어오는 요청을 해당 HandlerFunction으로 라우팅해 주는 역할을 합니다.
@RequestMapping과 비슷.

RounterFuntion은 요청을 위한 데이터뿐만 아니라 요청 처리를 위한 동작(HandlerFunction)까지 RouterFunction의 파라미터로 제공한다는 차이점이 있습니다.

## 17.3 함수형 엔드포인트에서의 reqeust body 유효성 검증
### Custom Validator를 이용한 유효성 검증
함수형 엔드포인트는 Spring의 Validator 인터페이스를 구현한 Custom Validator를 이용해 request body에 유효성 검증을 적용할 수 있습니다.

Spring MVC와의 차이점은 유효성 검증을 진행하는 로직 역시 Operator 체인 안에서 진행한다는 것입니다.

### 표준 Bean Validation을 이용한 유효성 검증
- Spring Validator 인터페이스 사용
- javax 표준 Validator 인터페이스 사용