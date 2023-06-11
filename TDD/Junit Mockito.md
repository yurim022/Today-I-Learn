# Junit - Mockito

### Mock

진짜 객체와 비슷하게 동작하지만 프로그래머가 직접 그 객체의 행동을 관리하는 객체

실제 구현체가 없거나, 있더라도 의존성을 줄이기 위해 사용

### [Mockito](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html)

Mock 프레임워크로, 객체를 쉽게 만들고 관리하고 검증할 수 있는 방법 제공 

- spring-boot-starter-test 를 사용하면 자동으로 포함됨
- 스프링부트를 사용하지 않는다면 의존성 직접 추가

```jsx
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>3.1.0</version>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-junit-jupiter</artifactId>
    <version>3.1.0</version>
    <scope>test</scope>
</dependency>
```

대체제: [EasyMock](http://easymock.org/), [JMock](http://jmock.org/)

<br><br>

# Mock 객체 만들기

### **StudyService.class**

```jsx
public class StudyService {

    private final MemberService memberService;

    private final StudyRepository repository;

    public StudyService(MemberService memberService, StudyRepository repository) {
        assert memberService != null;
        assert repository != null;
        this.memberService = memberService;
        this.repository = repository;
    }

    public Study createNewStudy(Long memberId, Study study) {
        Optional<Member> member = memberService.findById(memberId);
        if (member.isPresent()) {
            study.setOwnerId(memberId);
        } else {
            throw new IllegalArgumentException("Member doesn't exist for id: '" + memberId + "'");
        }
        Study newstudy = repository.save(study);
        memberService.notify(newstudy);
        return newstudy;
    }
```
<br><br>

### 1. Mockito.mock() 메소드

```java
import static org.junit.jupiter.api.Assertions.assertNotNull;
import static org.mockito.Mockito.mock;

@Test
    void createStudyService() {

        MemberService memberService = mock(MemberService.class);
        StudyRepository studyRepository = mock(StudyRepository.class);

        StudyService studyService = new StudyService(memberService, studyRepository);

        assertNotNull(studyService);
    }
```

- static import로 `import static org.mockito.Mockito.mock;` mock 함수를 가져옴
- mock(대상클래스) 를 통해 모킹

<br><br>

### 2. Mock 어노테이션

```java
@ExtendWith(MockitoExtension.class)
class StudyServiceTest {

    @Mock MemberService memberService;

    @Mock StudyRepository studyRepository;

@Test
    void createStudyService() {

        StudyService studyService = new StudyService(memberService, studyRepository);
        assertNotNull(studyService);
    }

}
```

- `@ExtendWith(MockitoExtension.class)` 와 함께 사용해야 제대로 Mock 객체를 만들 수 있음
- 클래스 내 여러 함수에서 Mock을 사용한다면 위와 같이 클래스에 전체적으로 `@Mock` 어노테이션으로 정의

```java
@ExtendWith(MockitoExtension.class)
class StudyServiceTest {
    
    @Test
    void createStudyService(@Mock MemberService memberService,
                            @Mock StudyRepository studyRepository) {
        StudyService studyService = new StudyService(memberService, studyRepository);
        assertNotNull(studyService);
    }
}
```

- 하나의 단위테스트에서만 사용하고 싶다면 매개변수로 받아줄 수도 있음

<br><br>

# Mock 객체 Stubbing

`Stubbing` 이란, Mock 객체의 행동을 조작하는 것

특정 값을 리턴하거나, 예외를 발생시킬 수 있다.

<br><br>

### Mock 객체의 default 행동

- 리턴값이 있는 것은 Null 리턴
- Primitive 타입은 기본 Primitive 타입 ex) int, double, boolean, char…
- 콜렉션은 비어있는 콜렉션
- Void 메소드는 예외를 던지지 않고 아무런 일도 발생하지 않음

<br><br>

## Mockito로 Stubbing

```java
    @Test
    @DisplayName("when ~ thenReturn 으로 맴버 stubbing 하기")
    void stubMemberTest() {

        Member member = new Member();
        member.setId(1L);
        member.setEmail("yurim@email.com");

        when(memberService.findById(1L)).thenReturn(Optional.of(member));

        Optional<Member> mockMember = memberService.findById(1L);
        assertEquals("yurim@email.com",mockMember.get().getEmail());

    }
```

- **when(mock객체 함수).thenReturn(리턴값**) 를 통해 어떤 행동을 할지 stub해줄 수 있음
- 이때 `when(memberService.findById(1L)).thenReturn(Optional.of(member))`  로 id가 1L인 데이터에 대해서만 stub해주었기 때문에 `memberService.findById(2L)` 은 assert 에러!

![image](https://github.com/yurim022/Today-I-Learn/assets/45115557/26b88c59-2db2-498f-92dc-7c59e91b7b8f)

<br><br>

## Mockito로 Stubbing - any

```java
		@Test
    @DisplayName("when ~ thenReturn 으로 any 맴버 stubbing 하기")
    void stubMemberAnyTest() {

        Member member = new Member();
        member.setId(1L);
        member.setEmail("yurim@email.com");

        when(memberService.findById(any())).thenReturn(Optional.of(member));

        assertEquals("yurim@email.com",memberService.findById(1L).get().getEmail());
        assertEquals("yurim@email.com",memberService.findById(2L).get().getEmail());

    }
```

- Mockito의 `ArgumentMatchers` 에 `any()` 를 넣어주면 어떤 인자값이 들어와도 같은 값을 반환함

![image](https://github.com/yurim022/Today-I-Learn/assets/45115557/b43f1eca-52d1-4fc4-b3d9-4201a3a44f58)


- ArgumentMatchers 로 다양한 동작을 컨드롤 할 수 있다. [Mockito 공식문서](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html#2) 참고

<br><br>

## Mockito로 Stubbing - doThrow

```java
		@Test
    @DisplayName("stub throw 에러")
    void stubMemberExceptionTest() {

        Member member = new Member();
        member.setId(1L);
        member.setEmail("yurim@email.com");

        doThrow(new IllegalArgumentException()).when(memberService).validate(1L);

        assertThrows(IllegalArgumentException.class, () -> {
            memberService.validate(1L);
        });
    }
```

- `doThrow(Exception.calss).when(mock객체).특정함수` 를 통해 특정 호출상황에 예외를 발생시킬 수 있음

<br><br>

## Mockito 메소드가 동일한 매개변수로 여러번 호출될 때 각기 다르게 행동하도록 조작

```java
		@Test
    @DisplayName("stub chaining")
    void stubMemberChaining() {

        Member member = new Member();
        member.setId(1L);
        member.setEmail("yurim@email.com");

        when(memberService.findById(any()))
                .thenReturn(Optional.of(member))
                .thenThrow(new RuntimeException())
                .thenReturn(Optional.empty());

        //첫번째 호출
        assertEquals("yurim@email.com",memberService.findById(1L).get().getEmail());

        //두번째 호출
        assertThrows(RuntimeException.class, () -> {
            memberService.findById(2L);
        });

        //세번째 호출
        assertEquals(Optional.empty(), memberService.findById(3L));
    }
```

- when(mock 함수).thenReturn().thenThrow().thenReturn(),,, 과 같이 계속해서 동작을 연결해주면 순서에 맞게 mock 함수가 호출될 때마다 다르게 동작
- 정의한 순서 이상으로 호출하면 마지막 정의한 stub으로 동작

<br><br>

## Mocking하여 스터디 만들기

```java
		
@ExtendWith(MockitoExtension.class)
class StudyServiceTest {

		@Mock MemberService memberService;

    @Mock StudyRepository studyRepository;

		@Test
    @DisplayName("createNewStudy")
    void createNewStudy() {

        //given
        StudyService studyService = new StudyService(memberService,studyRepository);
        Member member = new Member();
        member.setId(1L);
        member.setEmail("yurim@email.com");

        Study study = new Study(10, "테스트");

        when(memberService.findById(1L)).thenReturn(Optional.of(member));
        when(studyRepository.save(study)).thenReturn(study);

        //when
        studyService.createNewStudy(1L, study);

        //then
        assertEquals(member.getId(),study.getOwnerId());

    }
}
```

<br><br>

## Mockito - Verify

- 특정 메소드가 특정 매개변수로 몇번 호출되었는지

```java
verify(memberService, times(1)).notify(study);
verify(memberService, times(1)).notify(member);
```

- 특정 메소드가 특정 매개변수로 최소 한번/ 최대 한번 호출되었는지

```java
verify(memberService, atLeastOnce()).notify(study);
verify(memberService, atMostOnce()).notify(study);
```

- 특정 메소드가 특정 매개변수로 최소 한번/ 최대 x번 호출되었는지

```java
verify(memberService, atLeast(2)).notify(study);
verify(memberService, atMost(5)).notify(study);
```

- 특정 메소드가 특정 매개변수로 전혀 호출되지 않았는지

```java
verify(memberService, never()).validate(any());
```

- 어떤 순서대로 호출되었는지

```java
InOrder inOrder = inOrder(memberService);
        inOrder.verify(memberService).notify(study);
        inOrder.verify(memberService).notify(member);
```

- 특정시점 이후에 아무일도 벌어지지 않았는지

```java
verifyNoMoreInteractions(memberService);
```

<br><br>

# Mockito BDD 스타일

> **[BDD](https://en.wikipedia.org/wiki/Behavior-driven_development) (Behavior-driven development)**:
> 
> 
> 애플리케이션이 어떻게 행동해야 하는지에 대한 공통된 이해를 구성하는 방법
> 
> TDD에서 창안
> 

(여기에서 BDD에 대해 자세히 다루지는 x )

다만 BDD 스타일의 하나인 **Given/When/Then** 방식을 차용하여 테스트 코드 작성가능

**Mockito 는 [BddMockito](https://javadoc.io/static/org.mockito/mockito-core/3.2.0/org/mockito/BDDMockito.html) 라는 클래스를 통해 BDD 스타일의 API 제공**

- 기존 코드
    
    ```java
    		@Test
        @DisplayName("createNewStudyValidate")
        void createNewStudyBdd() {
    
            //given
            StudyService studyService = new StudyService(memberService,studyRepository);
            Member member = new Member();
            member.setId(1L);
            member.setEmail("yurim@email.com");
    
            Study study = new Study(10, "테스트");
    
            when(memberService.findById(1L)).thenReturn(Optional.of(member));
            when(studyRepository.save(study)).thenReturn(study);
    
            //when
            studyService.createNewStudy(1L, study);
    
            //then
            assertEquals(member.getId(),study.getOwnerId());
    
            verify(memberService, times(1)).notify(study);
            verify(memberService, times(1)).notify(member);
    
            verify(memberService, never()).validate(any());
    
            InOrder inOrder = inOrder(memberService);
            inOrder.verify(memberService).notify(study);
            inOrder.verify(memberService).notify(member);
    
            verifyNoMoreInteractions(memberService);
    
        }
    ```
    
    **Given / When /Then 과 mockito 함수 이름이 매핑되지 않음**
    

- When → Given

```java
given(memberService.findById(1L)).willReturn(Optional.of(member));
given(studyRepository.save(study)).willReturn(study);
```

- Verify → Then

```java
then(memberService).should(times(1)).notify(study);
then(memberService).shouldHaveNoMoreInteractions();
```

- BDD 적용코드

```java
		@Test
    @DisplayName("Bdd Style")
    void createNewStudyBdd() {

        //given
        StudyService studyService = new StudyService(memberService,studyRepository);
        Member member = new Member();
        member.setId(1L);
        member.setEmail("yurim@email.com");

        Study study = new Study(10, "테스트");

        given(memberService.findById(1L)).willReturn(Optional.of(member));
        given(studyRepository.save(study)).willReturn(study);

        //when
        studyService.createNewStudy(1L, study);

        //then
        assertEquals(member.getId(),study.getOwnerId());

        then(memberService).should(times(1)).notify(study);
        then(memberService).should(times(1)).notify(member);

        then(memberService).should(never()).validate(any());

        then(memberService).shouldHaveNoMoreInteractions();

    }
```
