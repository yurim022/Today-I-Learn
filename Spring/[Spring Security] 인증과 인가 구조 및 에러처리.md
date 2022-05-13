## SecurityWebFilter

Spring Webflux에서는 보안설정을 할때, SecurityWebFilter를 통해 SpringSecurity가 동작하는 동안 필터체인관리를 할 수 있다. 
SpringWebFilterOrder 순서에 맞게 필터가 적용이 되는데 순서는 다음과 같다. 

```

public enum SecurityWebFiltersOrder {
    FIRST(-2147483648),
    HTTP_HEADERS_WRITER,
    HTTPS_REDIRECT,
    CORS,
    CSRF,
    REACTOR_CONTEXT,
    HTTP_BASIC,
    FORM_LOGIN,
    AUTHENTICATION,
    ANONYMOUS_AUTHENTICATION,
    OAUTH2_AUTHORIZATION_CODE,
    LOGIN_PAGE_GENERATING,
    LOGOUT_PAGE_GENERATING,
    SECURITY_CONTEXT_SERVER_WEB_EXCHANGE,
    SERVER_REQUEST_CACHE,
    LOGOUT,
    EXCEPTION_TRANSLATION,
    AUTHORIZATION,
    LAST(2147483647);

    private static final int INTERVAL = 100;
    private final int order;

    private SecurityWebFiltersOrder() {
        this.order = this.ordinal() * 100;
    }

    private SecurityWebFiltersOrder(int order) {
        this.order = order;
    }

    public int getOrder() {
        return this.order;
    }
}
```


## SpringSecurityConfig

스피링세큐리티 관련 config 파일이다. 

```

@Configuration
@EnableReactiveMethodSecurity
@RequiredArgsConstructor
public class SecurityConfig {

    @Value("${jwe.secret-key}")
    String secretKey;

    @Value("${aica.encryption.enabled}")
    boolean encryptionEnabled;

    @Autowired(required = false)
    EncryptPayloadUtil encryptPayloadUtil;

    @Bean
    SecurityWebFilterChain springSecurityFilterChain(ServerHttpSecurity http) {

        http.authorizeExchange().pathMatchers(HttpMethod.OPTIONS).denyAll();
        http.authorizeExchange().pathMatchers(HttpMethod.HEAD).denyAll();
        http.authorizeExchange().pathMatchers(HttpMethod.PATCH).denyAll();
        http.authorizeExchange().pathMatchers(HttpMethod.TRACE).denyAll();

        http.authorizeExchange(exchange -> exchange
                .pathMatchers("/aica/api/v1/auth/**").permitAll()
                .pathMatchers("/aica/api/v1/guest/**").permitAll().   //permit하는 url
                .anyExchange().authenticated()). //위 url에 대해서 Authentication 제외
                .securityContextRepository(NoOpServerSecurityContextRepository.getInstance())
                .addFilterAt(new JwtTokenAuthenticationFilter(secretKey), SecurityWebFiltersOrder.HTTP_BASIC) //Jwt토큰 authFilter를 SecurityWebFiltersOrder.HTTP_BASIC에 실행시켜준다. 
                .httpBasic().disable()
                .formLogin().disable()
                .csrf().disable()
                .logout().disable()
                .cors().disable()
                .exceptionHandling().authenticationEntryPoint((exchange, exception) -> Mono.error(exception))
                .accessDeniedHandler((exchange, exception) -> Mono.error(exception));

        if (encryptionEnabled) {
            http.addFilterBefore(new PayloadSecurityFilter(encryptPayloadUtil), SecurityWebFiltersOrder.LAST);
        }

        return http.build();
    }

    @Bean
    public ReactiveUserDetailsService reactiveUserDetailsService() {
        return s -> Mono.empty();
    }
}


```

여기서 눈여겨 볼 점은

```
 http.exceptionHandling() //예외처리기능 작동
                .authenticationEntryPoint((exchange, exception) -> Mono.error(exception))   //인증실패시 처리
                .accessDeniedHandler((exchange, exception) -> Mono.error(exception));       //인가실패시 처리
                
```

이 두줄이다. 


![image](https://user-images.githubusercontent.com/45115557/150471549-de9af1a7-0bbb-4f3f-a96c-6ae32790474e.png)

ExceptionTranslationFilter는 2가지 종류의 예외를 처리하는데,
AuthenticationException은 인증실패시 예외를 발생시키고 AuthenticationEntryPoint 호출한다.
AccessDeniedException는 인가실패시 예외를 발생시키고 AccessDeniedHandler를 통해 예외처리를 한다.
 

## Custom 인증 Exception 발생

스프링 시큐리티에서 제공하는 필터를 사용해서 인증오류 발생시 에러를 발생시킨다. 

```java
public class JwtFilter extends OncePerRequestFilter {

	@Autowired
	private AuthenticationEntryPoint entryPoint;

	@Override
	protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filter) throws ServletException, IOException {

		try{
				//인증로직
		}catch(JWTException e){
		
			entryPoint.commence(request,response, new authException());

		}
	}

}
```

## 인증실패 처리

인증실패 처리는 **AuthenticationEntryPoint**를 통해 할 수 있다.
```java
public class AuthenticationErrorHandler implements AuthenticationEntryPoint {

	@Override
	public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException {
		
		//logging 등 에러처리
		
	}
}
```

참고링크
: https://fenderist.tistory.com/344 [Devman]
