# Generic 다루기

### vehicle을 구현한 Car class

```jsx
public abstract class Vehicle {
    private String name;
    private String manufacturer;
 
    // ... getters, setters etc
}
```

```jsx
public class Car extends Vehicle {
    private String engineType;
 
    // ... getters, setters etc
}
```

### 커스텀 어노테이션 만들기

```jsx
@Target({
  ElementType.FIELD, 
  ElementType.METHOD,
  ElementType.TYPE, 
  ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface CarQualifier {
}
```

- interface 어노테이션으로 커스텀 어노테이션을 만든다.
- Target 어노테이션을 통해 커스텀 어노테이션을 적용할 범위를 지정한다.
- Retension → 어노테이션 타입을 언제까지 보유할지 정하는 것. 자세한건 [https://sas-study.tistory.com/329](https://sas-study.tistory.com/329)
- Vehicle 을 상속받은 객체들을 구분하기 위해서 Qualifier 어노테이션을 붙여준다.

### 커스텀 어노테이션 만든것을 붙여서 @Qualifier 로 구분해준다.

```jsx
public class CustomConfiguration {
    @Bean
    @CarQualifier
    public Car getMercedes() {
        return new Car("E280", "Mercedes", "Diesel");
    }
}
```

```jsx
@Autowired
@CarQualifier
private List<Vehicle> vehicles;
```

참고링크

[https://www.baeldung.com/spring-autowire-generics](https://www.baeldung.com/spring-autowire-generics)

[https://sas-study.tistory.com/329](https://sas-study.tistory.com/329)
