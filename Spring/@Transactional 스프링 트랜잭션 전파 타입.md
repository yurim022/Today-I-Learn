## 스프링 트랜잭션 전파 타입

Propagation 즉, 전파타입이란 트랜잭션이 시작하거나 참여하는 방법에 관한 설정이다. 트랜잭션의 경계에서 트랜잭션이 어떻게 동작할지를 정하는 룰이라고 할 수 있다. 

```
public class ServiceA {

  private ServiceB serviceB;
  
  @Transactional
  public void a() {
    serviceB.b(); //a함수에서 b함수 호출
  }
  
  public class ServiceB {
  
    @Transactional
    public void b() {...}
  }

}

```

위와 같이 트랜잭션이 처리되는 과정 안에서 또다른 트랜잭션이 처리되는 경우가 있다. 
부모 트랜잭션이 있냐, 업ㅎ냐에 따라 타입별로 트랜잭션의 경계를 설정할 수 있다. 

![스크린샷 2022-08-08 오후 4 20 04](https://user-images.githubusercontent.com/45115557/183362040-bf38a14e-e778-41e5-9b59-3673baa6f52d.png)





출처:
https://www.baeldung.com/spring-transactional-propagation-isolation   
https://velog.io/@syleemk/%EB%A9%B4%EC%A0%91-%EB%8C%80%EB%B9%84-%EC%8A%A4%ED%94%84%EB%A7%81-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98-%EC%A0%84%ED%8C%8C   
https://velog.io/@kdhyo/JavaTransactional-Annotation-%EC%95%8C%EA%B3%A0-%EC%93%B0%EC%9E%90-26her30h
