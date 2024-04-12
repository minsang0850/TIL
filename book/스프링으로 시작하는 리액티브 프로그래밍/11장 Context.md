## 11.1 Context란?
>the situation, events, or information that are related to something and that help you to understand it.
 
'어떤 것을 이해하는 데 도움이 될 만한 관련 정보나 이벤트, 상황'

Context는 어떠한 상황에서 그 상황을 처리하기 위해 필요한 정보.

**예시**
- ServletContext: Servlet이 Servlet Container와 통신하기 위해 필요한 정보를 제공하는 인터페이스
- ApplicationContext: 애플리케이션의 정보(대표적으로 빈)를 제공하는 인터페이스
- SecurityContext: 애플리케이션 사용자의 인증 정보를 제공하는 인터페이스

**Reactor API 에서의 Context**
>A key/value store that is propagated between components such as operators via the context protocol.
 
Operator 같은 Reactor 구성요소 간에 전파되는 key/value 형태의 저장소.
여기서의 '전파'는 Downstream에서 Upstream으로 Conttext가 전파되어 Operator 체인상의 각 Operator가 해당 Context의 정보를 동일하게 이용할 수 있음을 의미합니다.

구독이 발생할 때마다 해당 구독과 연결된 하나의 Context가 생긴다.

### 컨텍스트에 데이터 쓰기
contextWrite() Operator의 파라미터는 Function 타입의 함수형 인터페이스인데,
람다 표현식으로 표현할 경우 람다 파라미터의 타입이 Context이고, 리턴 값 역시 Context인 것을 알 수 있습니다.

### Context에 쓰인 데이터 읽기
**원본 데이터 소스 레벨에서 읽는 방식**
defetContextual() 사용.  
deferContextual()은 defer() Operator와 같은 원리로 동작하는데, Context에 저장된 데이터와 원본 데이터 소스의 처리를 지연시키는 역할을 합니다.
deferContextual()의 파라미터로 정의된 람다 파라미터는 ContextView(Context가 아님)

**Operator 체인의 중간에서 읽는 방식**
transformDeferredContextual() Operator 사용.

Reactor에서는 Operator 체인상의 서로 다른 스레드들이 Context의 저장된 데이터에 손쉽게 접근할 수 있습니다.
그리고 Context.put()을 통해 Context에 데이터를 쓴 후에 매번 불변 객체를 다음 contextWrite() Operator로 전달함으로써 스레드 안전성을 보장합니다.

## 11.2 자주 사용되는 Context 관련 API
- put(key, value): key/value 형태로 Context에 값을 쓴다.
- of(key1, value2, key2, value2): key/value 형태로 Context에 여러 개의 값을 쓴다.
- putAll(ContextView): 현재 Context와 파라미터로 입력된 ContextView를 merge한다.
- delete(key): Context에서 key에 해당하는 value를 삭제한다.

**자주 사용되는 ContextView API**
- get(key): ContextView에서 key에 해당하는 value를 반환한다.
- getOrEmpty(key): ContextView에서 key에 해당하는 value를 Optional로 래핑해서 반환한다.
- getOrDefault(key, default value): ContextView에서 key에 해당하는 value를 가져온다. key에 해당하는 value가 없으면 default value를 가져온다.
- hasKey(key): key 존재 유무를 확인한다.
- isEmpty(): Context가 비어있는지 확인한다.
- size(): Context 내에 있는 key/value의 개수를 반환한다.

ContextView API는 Collection의 Map 사용 방식과 유사.

## 11.3 Context의 특징
**Context는 구독이 발생할 때마다 하나의 Context가 해당 구독에 연결된다.**
- Context는 Operator 체인의 아래에서 위로 전파된다.
  - 따라서 일반적으로 모든 Operator에서 Context에 저장된 데이터를 읽을 수 있도록 ContextWrite()을 Operator 체인의 마지막에 둡니다.
- 동일한 키에 대한 값을 중복해서 저장하면 Operator 체인에서 가장 위쪽에 위치한 contextWrite()이 저장한 값으로 덮어쓴다.

**Context는 인증 정보 같은 직교성(독립성)을 가지는 정보를 전송하는 데 적합합니다.**