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


### 3. 로그인 인증
SpringConifig 클래스에 AuthenticationManagerBuilder를 주입해서 인증에 대한 처리를 진행한다.
이때 메모리정보, JDBC, LDAP 등의 정보를 이용하여 인증처리가 가능하다.
아래는 인메모리를 사용하는 예제.


#### SpringConfig.java
```
@Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
        log.info("build Auth global.......");

        auth.inMemoryAuthentication()
                .withUser("manager")
                .password("{noop}1111")
                .roles("MANAGER");
    }
```

### 4. Spring Security Architecture

![image](https://user-images.githubusercontent.com/45115557/135797815-7575ace7-04c4-421e-acb9-8ea8f6824a66.png)

* 인증 메니저(Authentication Manager) 가 인증에 대한 실제적 처리 담당
* 인증 메니저가 인증과 관련된 정보를 UserDetailsService로 반환 (이 인터페이스를 구현하여 customizing 함)
* 특히 loadUser override


#### UserDetailService implements 예시
```
@Slf4j
@Service
@RequiredArgsConstructor
public class AccountService implements UserDetailsService {

    private final AccountRepository accountRepository;
    private final PasswordEncoder passwordEncoder;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        Account account = accountRepository.findByUserId(username)
                .orElseThrow(() -> new UsernameNotFoundException(username));
        return new User(account.getUserId(), account.getPassword(), authorities(account.getRoles()));
    }

    private Collection<? extends GrantedAuthority> authorities(Set<AccountRole> roles) {
        return roles.stream()
                .map(role -> new SimpleGrantedAuthority("ROLE_" + role.name()))
                .collect(Collectors.toList());
    }
}


```

* 모든 인증은 인증 매니저를 통해 이루어짐
* 인증 매니저를 생성하기 위해서는 인증 매니저 빌드를 이용
* 인증 매니저를 이용해 인증이라는 작업을 수행
* 인증 매니저들은 인증/인가를 위한 UserDetailsService를 통해서 필요한 정보들을 가져옵니다.
* UserDetails는 사용자의 정보 + 권한 정보들의 묶음


참고 링크
https://velog.io/@jayjay28/2019-09-04-1109-%EC%9E%91%EC%84%B1%EB%90%A8
