# 단위 테스트 작성 방법

*망나니 개발자님 블로그 글을 참고하여 정리한 글입니다. *

## 필요 라이브러리


* JUnit5
* AssertJ ; Junit에서 제공하는 메소드가 있지만 ex) assertEquals() 가독성이 떨어진다. 


## given / when / then 패턴

단위테스트에서 사용하는 패턴이다.
* given(준비) : 어떤 데이터가 준비되었을 때,
* when(실행) : 어떤 함수를 실행하면
* then(검증) : 어떤 결과가 나와야 한다. 

   추가적으로 verify로 어떤 메소드가 몇번 호출되었는지 검사하기도 하지만, 실용성이 낮아 메서드 호출 횟수가 중요한 테스트에서만 선택적으로 사용하면 된다고 한다!
   
```
@Displayname("testcode 메서드별 네임을 명시하고 싶을때")
@Test
void testExample(){

   //given

   //when

   //then

}
```
   기본적으로 이러한 구조로 단위 테스트가 진행된다. 


## Mockito 란

Mokito는 **개발자가 동작을 직접 제어할 수 있는 가짜(Mock) 객체를 지원하는 테스트 프레임워크** 이다. Spring으로 웹 애플리케이션을 개발하면서 생기는 여러 의존성이 생기게 되는데, 이는 단위테스트를 어렵게 한다. 이를 해결하기 위해 가짜 객체를 주입시켜주는 Mockito 라이브러리를 사용한다. **Mockito는 가짜 객체에 원하는 결과를 Stub하여 단위테스트를 진행할 수 있게 해준다.**


## Mockito 사용법

### 1. 객체 의존성 주입

* @Mock : Mock 객체를 만들어 반환
* @Spy : Stub 하지 않은 메소드를 원본 그대로 사용
* @InjectMocks : @Mock 또는 @Spy로 생성된 가짜 객체를 자동 주입시켜주는 어노테이션


### 2. Stub로 결과 처리

의존성 있는 객체는 가짜 객체를 주입하여 어떤 결과를 반환하라고 정해진 답을 준비시켜야 한다. Mockito에서는 다음과 같은 stub 메소드를 제공한다. 

* doReturn() : 특정 값 반환
* doNothing() : 아무것도 반환x
* doThrow() : 예외 발생


### 3. Junit과 Mockito 결합

기존의 Junit4에서는 @RunWith(MockitoJUnitRunner.class)를 붙여줘야 했는데, SpringBoot 2.2.0부터 공식적으로 JUnit5를 지원하게 되어 **@ExtenWith(MockitoExtension.class)** 를 명시해주면 된다.


   
## 컨트롤러 단위테스트

테스트 코드를 어떻게 구현하는지에 중점을 두었기 떄문에, 컨트롤러 로직은 제외하고 테스트 코드만 정리했다. 
더 자세한 코드를 보고 싶다면 [Junit과 Mockito 기반의 Spring 단위 테스트 코드 작성법](https://mangkyu.tistory.com/145) 을 참고하면 된다. 

```
@ExtendWith(MockitoExtension.class)
class UserControllerTest {

    @InjectMocks
    private UserController userController;

    @Mock
    private UserService userService;

    private MockMvc mockMvc;

    @BeforeEach
    public void init() {
        mockMvc = MockMvcBuilders.standaloneSetup(userController).build();
    }

}
```

컨트롤러 테스트를 위해서는 HTTP 호출이 필요하다. 스프링에서는 이를 위한 MockMVC를 제공하고 있다.    @BeforeEach에서 MockMvcBuilders.standaloneSetup(userController).build() 를 통해 MockMvc를 초기화 해준다. 


```
@DisplayName("회원가입 성공 테스트")
@Test
void signUpSuccess() throws Exception {

   // given
    SignUpRequest request = signUpRequest();
    UserResponse response = userResponse();
    
    doReturn(response).when(userService).signUp(any(SignUpRequest.class));
    
    // when
    ResultActions resultActions = mockMvc.perform(
        MockMvcRequestBuilders.post("/users/signUp")
                .contentType(MediaType.APPLICATION_JSON)
                .content(new Gson().toJson(request))
    );

    // then
    MvcResult mvcResult = resultActions.andExpect(status().isOk())
        .andExpect(jsonPath("email", response.getEmail()).exists())
        .andExpect(jsonPath("pw", response.getPw()).exists())
        .andExpect(jsonPath("role", response.getRole()).exists());
}

```

mockMvc로 HTTP 요청을 날려주고 결과를 ResultActions 객체로 받아준다. 
이 result 객의 andExpect을 통해 원하는 결과를 얻었는지 status, jsonPath 등으로 검증해준다. 


### @WebMvcTest

위와 같이 MockMvc를 생성하는 것은 번거롭다. SpringBoot는 컨트롤러 테스트를 위한 @WebMvcTest 어노테이션을 제공하고 있다. 이를 이용하면 MockMvc 객체가 자동으로 생성될 뿐만 아니라 ControllerAdvice, Filter, Interceptor 등 웹 테스트에 필요한 요소들을 모두 빈으로 등록해 스프링 컨텍스트 환경을 구성한다. @WebMvcTest는 스프링부트가 제공하는 테스트 환경이므로 @Mock 대신 @MockBean, @Spy 대신 @SpyBean을 사용해주면 된다. 

```
@WebMVcTest(UserController.class)
class UserControllerTest {

    @MockBean
    private UserService userService;

    @Autowired
    private MockMvc mockMvc;

    // 테스트 작성

}
```




## 서비스 계층 단위테스트

회원가입 성공 테스트를 진행해보자. 리포지토리 계층은 mock으로 만들어 doReturn 에서 원하는 응답값을 리턴하게 해주고, 의존성을 userService에 @InjectMocks 어노테이션으로 주입해주자.    실제 사용자의 비밀번호를 암호화 해야 하므로 엔코더는 @Spy로 실제 객체를 주입시켜 준다. 


```
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

   @InjectMocks
   private UserService userService;
   
   @Mock
   private UserRepository userRepository;
    
   @Spy
   private BCryyPasswordEncoder passwordEncoder;
   
   
   @Displayname("회원가입 테스트")
   @Test
   void signUp(){
   
      //given
      BCrytPasswordEncoder encoder = new BCryptPasswordEncoder();
      SignUpRequest request = signUpRequest();
      String encryptedPw = encoder.encode(request.getPw());
      
      doReturn(new User(request.getEmail(), encrytedPw, UserRole.ROLE_USER)).when(userRepository).save(any(User.class));
      
      //when
      UserResponse user = userService.signUp(request);
      
      //then
      assertThat(user.getEmail().isEqualTo(request.getEmail());
      asswerThat(encoder.matches(signUpDTO.getPw(),uesr.getPw())).isTrue();
     
   }
}
```


## 레포지토리 단위 테스트

### @DataJpaTest 

스프링부트에서는 JPA 레포지토리를 손쉽게 테스트 할 수 있는 @DataJpaTest 를 제공한다. 기본적으로 인메모리 데이터베이스인 H2를 기반으로 테스트용 데이터베이스를 구축하며, 테스트가 끝나면 트랜젝션을 롤백해준다. 레포지토리 계층은 실제 DB와 통신없이 단순 모킹하는 것은 의미가 없으므로 @DataJpaTest를 활용하자. 

```
@DataJpaTest
class UserRepositoryTest {

   @Autowired
   private UserRepository userRepository;
   
   @DisplayName("사용자 목록 조회")
   @Test
   void addUser() {
   
      //given
      userRepository.save(user());
      
      //when
      List<User> users = userRepository.findAll();
      
      //then
      assertThat(users.size()).isEqualTo(1);
      
   }
}
```








참고 링크:   https://mangkyu.tistory.com/144
           https://mangkyu.tistory.com/145
