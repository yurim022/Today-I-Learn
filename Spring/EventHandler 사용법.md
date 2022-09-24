# Spring 에서 Event Handler 사용법

먼저 특정상황을 가정해보자. 타 서비스와 연계해서 동작하는 서비스A와 서비스B가 있다.    
서비스A의 멤버 정보가 변할 경우 서비스B에 이를 알리도록 구현해보자. 

```

@Service
@RequiredArgsConstructor
public class MemberExecuteService implements ApplicationEventPublisherAware {

  private final ApplicationEventPublisher eventPublisher;
  
  @Transactional(propagation = Propagation.REQUIRES_NEW)
  public void memberExecute(CHANG_TYPE type, Member member) {
    eventPublisher.publishEvent(new MemberChangeEvent(type,member));
  }
}

```

