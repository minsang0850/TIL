
```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.formLogin()
        .loginPage("/loginPage") //사용자 정의 로그인 페이지
        .defaultSuccessUrl("/") //로그인 성공 후 이동 페이지
        .failureUrl("/loginPage") //로그인 실패 후 이동 페이지
        .usernameParameter("userId") //아이디 파라미터명 설정
        .passwordParameter("passwd") //패스워드 파라미터명 설정
        .loginProcessingUrl("/login_proc") //로그인 Form Action Url
        .successHandler(loginSuccessHandler()) //로그인 성공 후 핸들러
        .failureHandler(loginFailureHandler()); //로그인 실패 후 핸들러
    }
```

**로그 아웃**  
- 세션 무효화
- 인증 토큰 삭제
- 쿠키 정보 삭제
- 로그인 페이지로 리다이렉트

```java
protected void configure(HttpSecurity http) throws Exception {
     http.logout()   //로그아웃 처리
         .logoutUrl("") //로그아웃 처리 URL
         .logoutSuccessUrl("") //로그아웃 성공 후 이동페이지
         .deleteCookies()  //로그아웃 후 쿠키 삭제
         .addLogoutHandler() //로그아웃 핸들러
         .logoutSuccessHandler(); //로그아웃 성공 후 핸들러
}
```

**인증 API - Remember me 인증**  
1. 세션이 만료되고 웹 브라우저가 종료된 후에도 애플리케이션이 사용자를 기억하는 기능
2. Remember-me 쿠키에 대한 Http 요청을 확인한 후 토큰 기반 인증을 사용해 유효성을 검사하고 토큰이 검증되면 사용자는 로그인된다.
3. 사용자 라이프 사이클
   1. 인증 성공 (Remember-me 쿠키 설정)
   2. 인증 실패 (쿠키가 존재하면 쿠키 무효화)
   3. 로그아웃 (쿠키가 존재하면 쿠키 무효화)


```java
protected void configure(HttpSecurity http) throws Exception {
    http.rememberMe()
        .rememberMeParameter("remember") //기본 파라미터명은 remember-me
        .tokenValiditySeconds(3600) //Default는 14일
        .alwaysRemember(false) //remember me 기능이 활성화되지 않아도 항상 실행
        .userDetailsService();
}
```

**AnonymousAuthenticationFilter**  
- 익명 사용자 인증 처리 필터
- 익명사용자와 인증 사용자를 구분해서 처리하기 위한 용도로 사용
- 화면에서 인증 여부를 구현할 때 isAnonymous()와 isAuthenticated()로 구분해서 사용
- 인증 객체를 세션에 저장하지 않는다


**동시 세션 제어**  
```java
protected void configure(HttpSecurity http) throws Exception {
    http.sessionManagement()
        .maximumSessions(1)
        .maxSessionsPreventsLogin(true) //동시 로그인 차단함, false: 기존 세션 만료(default)
        .expiredUrl("/expired") //세연이 만료된 경우 이동할 페이지
        .invalidSessionUrl("/invalid"); //세션이 유효하지 않을 때 이동할 페이지
}
```

**세션 고정 보호**  
인증에 성공할 때마다 새로운 세션 생성
```java
protected void configure(HttpSecurity http) throws Exception {
    http.sessionManagement()
        .sessionFixation()
        .changeSessionId(); // none, migrateSession, newSession
}
```


**세션 정책**
- Always: 항상
- If required: 스프링 시큐리티가 필요시 생성(기본값)
- Never: 생성하지 않지만 이미 존재하면 사용
- Stateless: 스프링 시큐리티가 생성하지 않고 존재해도 사용하지 않음
```java
protected void configure(HttpSecurity http) throws Exception {
   http.sessionManagement()
           .sessionCreationPolicy(SessionCreationPolicy.IF_REQUIRED);
}
```

**인증 API -SessionManagementFilter**
1. 세션 관리
2. 동시적 세션 제어
3. 세션 고정 보호
4. 세션 생성 정책


**인증 API -ConcurrentSessionFilter**  
- 매 요청마다 현재 사용자의 세션 만료 여부 체크
- 세션 만료되었을 경우 즉시 만료 처리
- session.isExpired()==true
  - 로그아웃 처리
  - 즉시 오류 페이지 으답

**인가 API - 권한 설정**
- 선언적 방식
  - URL
    - http.antMatchers
  - Method
    - @PreAuthorize
- 동적 방식 - DB 연동 프로그래밍
  - URL
  - Method


