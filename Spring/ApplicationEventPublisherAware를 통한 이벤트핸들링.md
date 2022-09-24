# Spring 에서 Event Handler 사용법

먼저 특정상황을 가정해보자. 타 서비스와 연계해서 동작하는 서비스A와 서비스B가 있다.    
* 서비스A의 멤버 정보가 가입한 경우 서비스B에 이를 알리도록 구현해보자. 
* 단 서비스B에 알릴 때에는 비동기로 알리며, 누락되어도 신경쓰지 않는다. 
cf ) 보통 비동기 처리는 요청이 긴 경우, 로그 처리, 푸시 처리 등에 많이 사용된다. 

### ApplicationEventPublisherAware
`ApplicationEventPublisher`는 옵저버 패턴의 구현체이다. `ApplicationEventPublisherAware` 인터페이스를 구현하면 Spring이 자동으로 ApplicationEventPublisher를 주입해준다. 
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
  public void memberExecute(CHANGE_TYPE type, Member member) {
    
    memberMapper.join(member);
    eventPublisher.publishEvent(new MemberChangeEvent(type,member));
    
  }
}

```

#### 이벤트 정의
발생시킬 이벤트도 함께 정의해준다. 이벤트는 정보를 담고 있다. 

```java
@Data
public class MemberChangeEvent {

    private CHANGE_TYPE type;
    private Member member;

    public MemberChangeEvent(CHANGE_TYPE type, Member member) {
        this.woType = woType;
        this.member = member;
    }
}


```



### Async 등록

Spring에서 제공하는 `ThreadPoolTaskExecutor`를 사용하면 비동기방식의 별도 쓰레드로 작업을 위임해서 처리할 수 있다. 시간이 오래 걸릴 수 있는 작업들은 이를 활용하면 좋다. 
자세한 내용은 [Spring공식 ThreadpoolExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html)를 참고하자.   

#### ThreadPool 설정값
![image](https://user-images.githubusercontent.com/45115557/192103106-b5594f75-79e1-41db-8c53-ac304997b90d.png)   
표 출처: https://keichee.tistory.com/382


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

비동기 처리는 좀 더 딥하게 다뤄야 할 필요가 있으므로 여기에서는 간단하게 `@EnableAsync`를 통해 비동기 처리를 할 수 있다는 정도만 알아두자!


### Subscribe 한 이벤트 처리

```java

@RequiredArgsConstructor
@Service
public class EventSubscribeService {

private final RestTemplate restTemplate;

  @Async("memberChangedTaskExecutor")
  @EventListener
  public void handleMemberChangedEvent(MemberChangeEvent event){
  
    Member member = event.getMember();
    restTemplate.exchange(),,,, // 다른 서비스에 요청 보내기
    
  }
}

```
 `@EventListener` 어노테이션으로 이벤트 리스너임을 명시해 줄 수 있다. 
 `@Async("memberChangedTaskExecutor")`로 위에 등록했던 ThreadPoolExecutor가 별도 쓰레드를 만들어서 요청을 비동기로 처리하게 한다. 
 
 


참고링크:
https://atoz-develop.tistory.com/entry/Spring-ApplicationEventPublisher%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D   
https://kim-jong-hyun.tistory.com/104    
https://keichee.tistory.com/382     
https://velog.io/@jeongyunsung/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%ED%95%B4%EB%B6%80%ED%95%99-Async-EnableAsync-AsyncAnnotationBeanPostProcessor.     


