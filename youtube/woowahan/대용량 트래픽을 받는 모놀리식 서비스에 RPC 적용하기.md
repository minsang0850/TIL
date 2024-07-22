https://www.youtube.com/watch?v=iOoquUhKT5g

**가게노출 서비스**
코드가 복합해져 유지보수가 어려워짐.

간단하게 분리하자.

### 공통된 코드를 어떻게 관리할까?
- 공통된 코드를 lib로 제공해보자.
- lib를 npm으로 배포하자.
  - 저장소의 메모리 이슈
  - 개발자가 직접 라이브러리를 뜯어봐야 하는 상황이 생길 것 같음
- REST API 방식으로 제공해보자.
  - 데이터를 받아가기 위한 코드들이 각 클라이언트에서 독립적으로 작성해야 함.
  - API 버전 관리가 명확하지 못함.
- RPC 방식으로 제공해보자.
  - 클라이언트 코드 각각 구현할 필요 없음
  - 버전 관리 용이
  - 러닝 커브가 있음 (개발자들이 RPC에 익숙하지 않은 상태였기 때문)
  - RPC 코드를 잘 추상화하면 편하게 쓸 수 있을 것이라고 생각

### RPC 사전 지식
RPC(Remote Procedure Call)는 별도 원격 제어를 위한 코드 없이 다른 컴퓨터에 있는 프로시저(함수나 메서드)를 호출하는 프로세스를 가리킵니다. 

**함수 프로시저**
IDL(Interface Definition Language) - 서버와 클라이언트와의 약속

**구현체**
- gRPC(구글)
- Apache Thrift(페이스북)
- Finagle(트위터)

하나하나 IDL 파일을 생성하는게 관리도 어렵고, 힘듦.
-> 유지보수 어려움.

IDL을 만들기보단 기존 클래스와 인터페이스를 잘 활용하는 방안 고민.

Thrift 기반으로 구현을 하려고 함.
gRPC를 원하는 팀이 많아서
사용하는 개발자들이 RPC 구현체를 몰라도 되도록 구현.

친숙하게 하기 위해 Spring의 사용성과 비슷하게 생성.

**WoowaBoot 요구사항**
- 기존 자바 클래스를 최대한 활용하여 유지보숙 ㅏ편하도록
- RPC 구현체를 선태갛여 사용할 수 있도록
- 기존 Spring의 사용성과 크게 차이가 없도록

**RestController를 RPC가 가능한 컨트롤러로 변경**
- @WoowaBootController 어노테이션 추가
- implements SampleService

**서버 시작하기**
- WoowaBootApplication(service = ServiceInfo.SERVICE, role = ServiceInfo.ROLE, module = WoowaModuleType.Thrift)

**IDL**
자바 인터페이스가 IDL 역할

**Client**
WoowaRpcFactory에 DNS나 IP 세팅.
생성된 팩토리에 인터페이스를 전달하여 client를 생성.

**구현 코드(Client**
```java
SampleService sampleService = sampleClientType1.getSampleServiceClient();
EchoResponse echoResponse = sampleService.echo(new EchoRequest("hello"));
```

**전체 구현**
- 프로토콜(인터페이스)이 정의되면
- 서버는 구현
- 클라이언트는 호출

**서비스 메쉬**
- L7
- L4
- DNS

## Woowa하게 RPC 적용하기
### RPC로 만들 Bean 추출하여 Lib로 변경
- 추출된 Lib를 include하여 서버를 구동.
- 추출된 Lib를 include하여 RPC 서버 구동.
- Lib와 RPC의 결과가 같음을 테스트하고 두 가디를 모두 가진 서버 배포. 선택적으로 lib와 rpc를 스위칭


