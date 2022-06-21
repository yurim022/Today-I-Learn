## SpringBoot TEST

우선 TEST는 Junit5를 기준으로 공부해보았다. !!
중요한 어노테이션은 세개!
@Test 
@BeforeEach 
@AfterEach  

```jsx
@Test
public void testMethodA(){}

@Test
public void testMethodB(){}
```

- 동작 순서

@BeforeEach → testMethodA → @AfterEach 

@BeforeEach → testMethodB→ @AfterEach 

**application.properties**

```yaml
#test property setting
author.name=TESTER
author.age=27
```

**PropertiestTest.java**

```java
@SpringBootTest
public class PropertiesTest {

    @Autowired
    Environment environment;

    @Test
    public void testMethod(){
        System.out.println("이름 : " + environment.getProperty("author.name"));
        System.out.println("나이 : " + environment.getProperty("author.age"));
        System.out.println("국가 : " + environment.getProperty("author.nation"));
    }
}
```

**결과**

<img width="591" alt="스크린샷 2022-06-21 오후 5 18 18" src="https://user-images.githubusercontent.com/45115557/174759577-d221b4df-e31c-42f5-b4ce-82a65312d082.png">

