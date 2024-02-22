
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

