# 스프링 부트 장점

- 라이브러리 관리 자동화

maven도 관리 자동화는 해준다,,,(빌드과정 - jar말아주기 등) 

springbootstarter가 라이브러리 버전관리까지 해줌! 

- 내장 서버 (tomcat) 설정 간소화   - embeded server
- 독립적으로 실행 가능한 JAR



# Springboot 에서 container의 역할

- 객체생성
- 의존성 관리
    
    DI (Dependency Injection) 
    
    @ Autowired. 주로씀 (@ Qulifier, @ Resource )
    


# WebApplicationType 설정

**MainApplication.class**

```jsx
SpringApplication application = new SpringApplication(Chapter01Application.class);
        application.setWebApplicationType(WebApplicationType.SERVLET);
        application.run(args);
```

`WebApplicationType.*SERVLET` → default 설정, tomcat*

`WebApplicationType.NONE` → *Java로 시작, tomcat 구동(x)*

`WebApplicationType.REACTIVE` → *webflux 등 비동기처리*

**application.properties**

설정파일이 우선순위를 가짐

```jsx
spring.main.web-application-type=servlet

server.port=8080 #포트번호
```

#### cf. RestController의 장점

Controller는 MVC 패턴에 의해 View를 만드려고 데이터를 보내는데, RestController를 쓰면 View 없이 API 로 쓸 수 있다. 



# 자동 설정 

커스텀 객체 → 스프링 제공 객체 순 동작

 **`@ ComponentScan`**

- 사용자가 정의한 객체를 초기화

`@ **EnableAutoConfiguration**`

- 스프링에서 제공하는 객체

### run 시퀀스

1. `@SpringBootApplication` 이 붙은 어플리케이션 실행
2.  `@Component` 어노테이션이 있는 클래스들을 스캔해서 Bean등록
3. `@EnableAutoConfiguration` 에 의해 `spring.factories` 안에 들어있는 수많은 자동 설정이 조건에 따라 적용
