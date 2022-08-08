## 스프링 트랜잭션 전파 타입

Propagation 즉, 전파타입이란 **트랜잭션이 시작하거나 참여하는 방법에 관한 설정**이다. 트랜잭션의 경계에서 트랜잭션이 어떻게 동작할지를 정하는 룰이라고 할 수 있다. 

```
public class ParentService {

  private ChildService childService;
  
  @Transactional
  public void parent() {
  
    childService.child();
    
  }
  
  public class ChildService {
  
    @Transactional
    public void child() {...}
    
  }

}

```

위와 같이 트랜잭션이 처리되는 과정 안에서 또다른 트랜잭션이 처리되는 경우가 있다. 


부모 트랜잭션이 있냐, 없냐에 따라 타입별로 트랜잭션의 경계를 설정할 수 있다. 
@Transactional 을 클래스, 인터페이스, 또는 메서드 레벨에 명시하면 해당 메서드 호출 시 지정된 트랜잭션이 작동하게 된다. 해당 클래스의 Bean이 다른 클래스의 Bean에서 호출된 경우에만 작동한다. *같은 Bean 내에서 @Transactional이 명시된 다른 메서드를 호출해도 동작하지 않는다.*


### Spring 전파타입
![스크린샷 2022-08-08 오후 4 20 04](https://user-images.githubusercontent.com/45115557/183362040-bf38a14e-e778-41e5-9b59-3673baa6f52d.png)





출처:
https://www.baeldung.com/spring-transactional-propagation-isolation   
https://velog.io/@syleemk/%EB%A9%B4%EC%A0%91-%EB%8C%80%EB%B9%84-%EC%8A%A4%ED%94%84%EB%A7%81-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98-%EC%A0%84%ED%8C%8C   
https://velog.io/@kdhyo/JavaTransactional-Annotation-%EC%95%8C%EA%B3%A0-%EC%93%B0%EC%9E%90-26her30h
