## Spring Security Config


### 1. 먼저 의존성을 추가한다. 

```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-security</artifactId>
</dependency>

```



### 2. Config 클래스를 작성한다. 
```
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers("/guest/**").permitAll()
                .antMatchers("/manager/**").hasRole("MANAGER")
                .antMatchers("/admin/**").hasRole("ADMIN");

        http.formLogin();
        http.exceptionHandling().accessDeniedPage("/accessDenied");
        http.logout().logoutUrl("/logout").invalidateHttpSession(true);

    }

```

* 특정한 경로에 특정한 권한을 가진 사용자만 접근할 수 있도록 아래 메소드 사용
  * http.authorizeRequest()  시큐리티 처리에 HttpServletRequest를 이용
  * antMatchers() 특정한 경로 지정
  * permitAll() 모든 사용자가 접근
  * hasRole() 특정 권한을 가진 사람만 접근 가능

* http.formLogin() 은 form 태그 기반의 로그인을 지원하겠다는 설정
	* /login 경로를 호출하면 스프링 시큐리티에서 제공하는 기본 로그인 화면을 볼 수 있음

* spring security가 웹을 처리하는 기본 방식은 HttpSession
	* 브라우저가 완전히 종료되면 로그인한 정보를 잃게 됨
	* 브라우저를 종료하지 않을 때, 로그아웃을 행해서 로그인한 정보 삭제
	* http.logout().invalidateHttpSession(true)
