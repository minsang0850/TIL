https://www.youtube.com/watch?v=yqSrRQOxQr0

# API
Application Programming Interface의 약자로 소프트웨어 애플리케이션들이 서로 상호작용할 수 있도록 만들어진 인터페이스를 의미합니다.

# HTTP 프로토콜
웹에서 정보를 주고받기 위한 표준 프로토콜 중 하나입니다.

**요청**
- 메서드
- URL
- 헤더
- 바디

**응답**
- 상태 코드
- 헤더
- 바디

# RESTful 하다?
## 저으이
자원을 표현하고 상태를 전소앟기 위한 규칙과 제약을 따르는 웹 서비스 디자인 스타일입니다.

## 규칙
- 자원 식별자(URI): 각 자원은 고유한 URI를 가져야합니다. URI는 명사로 표현되며, 자원을 식별할 수 있도록 명확하고 직관적으로 정의되어야 합니다.
- 표현(Representation): 자원의 표현은 클라이언트와 서버 간에 전송되는 형태입니다. JSON, XML, TEXT 등이 있습니다.
- 상태 전송(Stateless): 각 요청 간에 클라이언트의 상태를 서버에 저장하지 않습니다.
- 자기 서술적 메시지(Self-descriptive Messages): 각 메시지는 자기 설명적이어야 하며, 어떻게 처리해야 하는지 명확하게 표현되어야 합니다.

### 예시
- 모든 글 가져오기(GET): /posts
- 특정 글 가져오기(GET): /posts/{id}
- 글 작성하기(POST): /posts
- 글 수정하기(PUT): /posts/{id}
- 글 삭제하기(DELETE): /posts/{id}

### 특징
- 요청에 Body 존재
- 성공 응답에 Body 존재
- 안전성
- 멱등성(Idenpotent): 동일한 요청을 여러 번 반복해서 수행하더라도 결과가 동일하게 나오는 특성을 의미합니다.
  - PUT: 멱등성 O
  - POST: 멱등성 X
- 캐시여부: 캐시에 저장할 수 있는지 여부.



