# @Tag

Junit에서는 테스트 그룹을 만들고 원하는 테스트 그룹만 테스트를 실행할 수 있는 기능 제공

```java
		@Test
		@Tag("develop")
    @DisplayName("스터디만들기 develop")
    void createStudyServiceMockWithMethod() {
        MemberService memberService = mock(MemberService.class);
        StudyRepository studyRepository = mock(StudyRepository.class);
        StudyService studyService = new StudyService(memberService, studyRepository);
        assertNotNull(studyService);
    }
```

- 테스트 메소드에 태그 추가
- 하나의 테스트 메소드에 여러 태그를 사용가능

![image](https://github.com/yurim022/Today-I-Learn/assets/45115557/c7799972-65d8-4493-a0a9-b43e09aaf500)

- edit configuaration 에 들어가서 Tag기반의 테스트 configuartion을 추가

![image](https://github.com/yurim022/Today-I-Learn/assets/45115557/2a3cea5b-bd79-49b7-b019-cb2b32cffa1b)


- 아래와 같이 태그가 붙은 것만 동작

</br></br>

# 커스텀 태그

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@Test
@Tag("prod")
public @interface ProdTest {
}
```

- Junit5 어노테이션을 조합하여 커스텀 태그 생성 가능

```java
@Test
    @DisplayName("prod custom tag")
    @ProdTest
    void stubMemberAnyTest() {

        Member member = new Member();
        member.setId(1L);
        member.setEmail("yurim@email.com");

        when(memberService.findById(any())).thenReturn(Optional.of(member));

        assertEquals("yurim@email.com",memberService.findById(1L).get().getEmail());
        assertEquals("yurim@email.com",memberService.findById(2L).get().getEmail());

    }
```

- 커스텀 어노테이션을 각 테스트 위에 붙여주어 오타로 인한 테스트 누락을 막을 수 있음
