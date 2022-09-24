# Spring 에서 Event Handler 사용법

먼저 특정상황을 가정해보자. 타 서비스와 연계해서 동작하는 서비스A와 서비스B가 있다.    
* 서비스A의 멤버 정보가 가입한 경우 서비스B에 이를 알리도록 구현해보자. 
* 단 서비스B에 알릴 때에는 비동기로 알리며, 누락되어도 신경쓰지 않는다. 

### ApplicationEventPublisherAware
**ApplicationEventPublisher**는 옵저버 패턴의 구현체이다. ApplicationEventPublisherAware 인터페이스를 구현하면 Spring이 자동으로 ApplicationEventPublisher를 주입해준다. 
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


### Async 등록

Spring에서 제공하는 **ThreadPoolTaskExecutor**를 사용하면 비동기방식의 별도 쓰레드로 작업을 위임해서 처리할 수 있다. 시간이 오래 걸릴 수 있는 작업들은 이를 활용하면 좋다. 

```java

@Data
@Configuartion
@EnableAsync
@ConfigurationProperties(prefix="async")
public class AsyncConfig implements AsyncConfigurer {

  private AsyncTaskProperties memberChange;
  
  
  @Data
  public static class AsyncTaskProperties {
    
    int corePoolSize;
    int maxPoolSize;
    int queueSize;
  
  }
  
  @Bean(name = "memberChangedTaskExecutor")
  public Executor memberChangedTaskExecutor() {
  
    ThreadPoolTaskExecutor memberChangedTaskExecutor = new ThreadPoolTaskExecutor();
    memberChangedTaskExecutor.setCorePoolSize(memberChange.getCorePoolSize());
    memberChangedTaskExecutor.setMaxPoolSize(memberChange.getMaxPoolSize());
    memberChangedTaskExecutor.setQueueCapacity(memberChange.getQueueSize());
    memberChangedTaskExecutor.setThreadNamePrefix("MEMBER-CHANE-THREAD-");
    memberChangedTaskExecutor.setRejectedExecutionHandler(new ThreadPoolExecutor.AbortPolicy());
    memberChangedTaskExecutor.initialize();
    return memberChangedTaskExecutor; 
  
  }
}


```


@@ConfigurationProperties(prefix="async") 를 통해 application.yml 파일의 정보를 가져와서 매핑해준다. 
```
async:
  member-change:
    corePoolSize: 1
    max-pool-size: 100
    queue-size: 0
```




참고링크:
https://atoz-develop.tistory.com/entry/Spring-ApplicationEventPublisher%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D   
https://kim-jong-hyun.tistory.com/104   
