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
















참고링크: 
https://www.baeldung.com/integration-testing-in-spring
https://we1cometomeanings.tistory.com/65
https://velog.io/@gidskql6671/Spring-Boot-SpringBootTest%EC%99%80-WebMvcTest

