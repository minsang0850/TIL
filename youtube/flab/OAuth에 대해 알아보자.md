# 인증과 인가
## 인증
Authentication: 식별 가능한 정보를 바탕으로, 서비스에 등록된 유저의 신원을 입증하는 과정
= 아이디 및 비밀번호를 바탕으로, 게시판 서비스에 등록된 유저인지 확인하는 과정
= 회원가입 및 로그인

## 인가
Authorization: 인증된 유저에게 특정 리소스나 기능에 접근할 수 있는 권한을 부여하는 과정
= 글 작성, 타인의 글 조회 권한을 부여

# OAuth와 소셜 로그인
서비스에서 구글(네이버, 카카오, ..) ID, PW를 관리하는 것은 부담

## OAuth
서비스가 직접 사용자의 아이디 및 비밀번호를 관리하는게 아니라, 대신 관리해주는 일종의 대리자
서비스는 OAuth를 통해 액세스 토큰을 얻고, 리소스 서버에 접근하여 데이터를 조작할 수 있는 권한을 얻는다.

### OAuth Role
- Resource Owner: 인증을 수행하는 주체
- Client: 권한을 위임받은 주체
- Resource Server: 인가 수행 및 리소스 제공
- Authorization Server: 인증 검증 및 권한 부여

### OAuth Seetting
Resource Server에서 Clinet를 등록할 때 필요한 정보
1. Client ID: 서비스 식별자
2. Client Password: 서비스 검증용
3. redirect URL: 서비스가 권한을 받는 통로 또는 Authorization code를 부여받는 주소

### OAuth와 소셜 로그인
OAuth는 인가를 위한 기술, 소셜 로그인은 인증을 위한 기술

**OAuth 과정**
- 구글 연동 로그인 페이지 제공
- 구글 로그인 클릭 시, redirect URL + scope + client ID 링크 제공
- 구글 로그인 시도
- Resource Server는 Owner에게 Authorization Code를 부여
  - client에게 전달
  - client <-> Resource Server 간 Authorization, client ID, client secret, redirect URL 를 통해 인증
  - Resource Server -> client: Access Token을 부여

**소셜 로그인 과정**
- Access Token을 통해 유저를 식별할 수 있는 정보를 바등ㅁ
- 해당 profile을 통해 멤버 생성 또는 조회
- 식별 정보를 활용하여 JWT를 만든 후 반환

# Google OAuth 2.0 구현
## JWT
JSON Web Token: 요청과 응답 안에 사용자의 content를 담아서 stateless하게 사용자의 인증과 인가를 처리

1. 클라이언트는 서버에 요청을 보낸다.
2. 서버는 Secret Key를 이용하여 JWT를 생성한다.
3. 헤더에 JWT를 담아서 클라이언트에게 응답한다.
4. 다음 요청부터 클라이언트는 JWT를 헤더에 담아서 요청을 보낸다.
5. 서버는 Secret Key를 이용하여 JWT를 검증한다.
6. JWT를 디코딩하여, 유저의 정보를 파악하고 만료 시기 및 권한을 확인한다.
7. 유효성을 통과하였다면, 인증된 토큰임을 확인한다.

**특징**
- 디코딩하기 쉽기 떄문에, 비밀번호 같은 중요한 정보는 담지 않는다.
- 서버가 여러 대 있어도 모든 서버가 시크릿 키를 가지고 있기 떄문에, 확장성이 높아진다.
- 액세스 토큰이 탈취되었을 경우, 사용자의 권한을 이용할 수 있기 때문에 만료 기한을 지정한다.

## Refresh 토큰
1. 로그인 요청에 따라 Secret Key를 통해 토큰을 생성
2. Access Token과 Refresh Token을 한 번에 생성
3. Refresh Token을 저장소에 저장
4. 두 토큰을 한 번에 클라이언트에 보내서 저장
5. Access Token을 사용해서 클라이언트가 요청을 보냄
6. Access Token이 만료되면, 클라이언트는 Access Token과 Refresh Token을 함께 보내서 검증
7. 이후 Refresh Token 새로 갱신

**특징**
- 토큰으로 상태 관리를 하기에 세션을 둘 필요가 없다.
- 토큰도 탈취당할 수 있기에, 토큰 관리를 해야 한다.