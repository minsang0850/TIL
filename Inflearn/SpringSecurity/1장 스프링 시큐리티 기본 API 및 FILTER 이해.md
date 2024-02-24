
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