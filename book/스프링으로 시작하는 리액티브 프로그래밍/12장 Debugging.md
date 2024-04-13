## 12.1 Reactor에서의 디버깅 방법
### 12.1.1 Debug Mode를 사용한 디버깅
Reactor에서는 디버그 모드(Debug Mode)를 활성화해서 Reactor Sequence를 디버깅할 수 있습니다.

Hooks.onOperatorDebug()로 디버그 모드 활성화.

Operator 체인이 시작되기 전에 디버그 모드를 활성화하면 에러가 발생한 지점을 좀 더 명확하게 찾을 수 있습니다.

**한계**
디버그 모드의 활성화는 비용이 많이 드는 동작 과정을 거칩니다.
- 애플리케이션 내에 있는 모든 Operator의 스택트레이스를 캡처한다.
- 에러가 발생하면 캡처한 정보를 기반으로 에러가 발생한 Assembly의 스텍트레이스를 원본 스택트레이스 중간에 끼워 넣는다.

따라서 에러 원인을 추적하기 위해 처음부터 디버그 모드를 활성화하는 것은 권장하지 않습니다.

> 여기서 Assembly란 Operator에서 리턴하는 새로운 Mono 또는 Flux가 선언된 지점을 의미함

### 12.1.2 checkpoint() Operator를 사용한 디버깅
checkpoint() Operator를 사용하면 특정 Operator 체인 내의 스택트레이스만 캡처합니다.

**TraceBack을 출력하는 방법**  
checkpoint()를 사용하면 실제 에러가 발생한 assembly 지점 또는 에러가 전파된 assembly 지점의 traceback이 추가됩니다.

**Traceback 출력 없이 식별자를 포함한 Description을 출력해서 에러 발생 지점을 예상하는 방법**  
checkpoint(description)을 사용하면 에러 발생 시 Traceback을 생략하고 description을 통해 에러 발생 지점을 예상할 수 있습니다.

**Traceback과 Description을 모두 출력하는 방법**  
cehckpoint(description, forceStackTrace)를 사용.

### 12.1.3 log() Operator를 사용한 디버깅
log()  Operator는 Reactor Sequence의 동작을 로그로 출력하는데, 이 로그를 통해 디버깅이 가능합니다.

cancel() 시그널이 출력되면
-> 데이터를 emit하지만 처리 중에 에러가 발생했음을 의미합니다.

log("Fruit.Substring", Level.FINE)  
- Fruit.Substring: 카테고리
- Level.FINE: Java에서 지원하는 로그 레벨 (Slf4j 로깅 프레임워크 기준 DEBUG)



### IntelliJ에서 Reactor Sequence 디버깅 지원
File > Settings  
Languages & Frameworks > Reactive Streams  
Hooks.onOperatorDebug 선택.
