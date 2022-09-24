# Spring 에서 Event Handler 사용법

먼저 특정상황을 가정해보자. 타 서비스와 연계해서 동작하는 서비스A와 서비스B가 있다.    
서비스A의 멤버 정보가 가입한 경우 서비스B에 이를 알리도록 구현해보자. 

### ApplicationEventPublisherAware
ApplicationEventPublisher는 옵저버 패턴의 구현체이다. ApplicationEventPublisherAware 인터페이스를 구현하면 Spring이 자동으로 ApplicationEventPublisher를 주입해준다. 
publishEvent 함수를 통해 Event를 발행할 수 있다. 


```java

@Service
@RequiredArgsConstructor
public class MemberExecuteService implements ApplicationEventPublisherAware {

  private final MemberMapper memberMapper; 
  private ApplicationEventPublisher eventPublisher;
  
  @Override
  public void setApplicationEventPublisher(ApplicationEventPublisher applicationEventPublisher) {
    this.eventPublisher = applicationEventPublisher;
  }
  
  
  @Transactional(propagation = Propagation.REQUIRES_NEW)
  public void memberExecute(CHANG_TYPE type, Member member) {
    
    memberMapper.join(member);
    eventPublisher.publishEvent(new MemberChangeEvent(type,member));
    
  }
}

```



참고링크:
https://atoz-develop.tistory.com/entry/Spring-ApplicationEventPublisher%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D   
