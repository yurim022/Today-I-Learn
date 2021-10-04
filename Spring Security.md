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
  * http.authorizeRequest()  시큐리티 처리에 HttpServletRequest를 이용한다는 것을 의미합니다.
  * 

