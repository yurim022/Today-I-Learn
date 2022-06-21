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

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f8ed9d66-f273-4c2d-80b2-95bc4a3177bc/Untitled.png)
