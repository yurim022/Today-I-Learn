# 스프링에서 통합테스트 방식 비교

먼저 ***MockMVC***와 ***RestTemplate*** 두 방식을 비교하기 전에, 두 방식에 대해 알아보고자 한다. 
이 방식은 모두 통합테스트 방식에 속한다. 

## MockMVC
MVC 프로젝트를 테스트 할 때, 컨트롤러 테스트를 쉽게 하기 위해 요청을 수행하고 응답을 만들어내는 Mock 객체를 사용한다. 

테스트를 위해 실제 객체와 비슷한 객체를 만드는 것을 모킹(Mocking)이라고 하고, 테스트 하려는 객체가 복잡한 의존성을 가지고 있을 때, 모킹한 객체를 이용하면 의존성을 단절시킬 수 있어 쉽게 테스트 가능하다. 

### 의존성 추가

```
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### @SpringBootTest

스프링에서 MockMVC를 사용하는 방식은 두가지가 있는데, 먼저 @SpringBootTest 부터 살펴보자. 

```
@SpringBootTest
@AutoConfigureMockMvc
public class MyControllerTest {
    @Autowired
    private MockMvc mockMvc;
}
```
 
@SpringBootTest는 통합 테스트를 위한 어노테이션이다. 
단위테스트와 같이 기능검증을 위한 것이 아니라 spring framework에서 전체 플로우가 제대로 동작하는지 테스트하기 위함이기 때문에, Controller 뿐만아니라 service, repository 등 모든 스프링 빈을 등록한다. 모든 Bean을 로드하기 때문에 시간이 오래걸리고 무겁다.    
반면 다 불러오는 만큼, 운영환경과 가장 유사한 테스트가 가능하다.    
* 주의사항 : 각자 서로의 MockMVC를 모킹하기 때문에, @WebMvcTest와 같이 사용할 수 없다. 

```
@ActiveProfiles("dev")
```
프로파일 전략을 사용 중이라면 원하는 프로파일 환경값을 설정 가능하다. 


### @WebMvcTest (단위테스트)

다음은 통합테스트는 아니지만, 단위테스트에서 Mock을 사용하는 @WebMvcTest이다.    
@WebMvcTest는 @Controller, @ControllerAdvice 와 같은 Web레이어와 관련된 특정 빈들만 등록하기 때문에 비교적 가볍다. 

```
@WebMvcTest(controllers = MyController.class)
public class MyControllerTest {
    @Autowired
    private MockMvc mockMvc;
    
     @MockBean
    private MyService myService;
}
```
Web 관련 레이어만 등록하기 때문에, Service 빈은 등록되지 않으므로 가짜 @MockBean을 통해 만들어 줄 수 있다.   













참고링크: 
https://www.baeldung.com/integration-testing-in-spring   
https://we1cometomeanings.tistory.com/65   
https://velog.io/@gidskql6671/Spring-Boot-SpringBootTest%EC%99%80-WebMvcTest

