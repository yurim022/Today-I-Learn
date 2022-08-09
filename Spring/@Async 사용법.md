# @Async 사용법


## Java 비동기 처리

Spring의 비동기를 보기 전에, 순수 Java 비동기 처리 방식을 알아보자. 

```
public class MessageService {

    public void print(String message) {
        System.out.println(message);
    }
}

public class Main {

    public static void main(String[] args) {
        MessageService messageService = new MessageService();

        for (int i = 1; i <= 100; i++) {
            messageService.print(i + "");
        }
    }
}
```
단순히 message를 받아서 출력하는 기능을 동기 방식으로 처리한다면 다음과 같다. 


```
public class MessageService {

    public void print(String message) {
        new Thread(() -> System.out.println(message))
                .start();
    }
}
```

해당 기능을 비동기로 처리하려면 Thread를 생성해서 작성할 수 있는데, 이는 매우 위험한 방식이다. Thread를 관리할 수 없기 때문이다. 
**Thread 생성 비용이 크며, 동시에 많은 요청이 왔을경우 OOM(Out Of Memory) 에러가 날 수 있다.**
따라서 ThreadPool를 구현해서 미리 생성된 Thread로 요청을 처리하는 것이 효율적이다. Java에서는 ThreadPool을 구현하기 위해 ExecutorService 클래스를 제공하고 있다. 


```
public class MessageService {
  
  private final ExecutorService executorService = Executots.newFixedThreadPool(10);
  
  public void print(String message) {
    executorService.submit(()0> Sysyem.out.println(message));
  }
}


public class Main {

  public static void main(String[] args) {
        MessageService messageService = new MessageService();

        for (int i = 1; i <= 100; i++) {
            messageService.print(i + "");
        }
    }

}

```

전체 스레드의 개수를 10개로 제한하고 있고, 멀티 스레딩 방식의 비동기 처리도 할 수 있게 되었다. 하지만 위 방식은 비동기 방식으로 처리하고 싶은 메소드마다 ExecutorService의 submit() 메소드를 적용해야 하므로 반복적인 수정작업이 필요하다. 처음에 동기로 작성한 메소드를 비동기로 바꾸고 싶다면 메소드 자체의 로직을 변경해야 하는 불편함이 있다. 


## Spring @Async without ThreadPool (비추)

```
@EnableAsync
@SpringBootApplication
public class AsyncApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

먼저 첫번째 방식은 @EnableAsync 어노테이션을 Application 클래스에 붙여주고, 비동기 방식으로 처리하고 싶 동기 로직의 메소드 위에 @Async를 붙이는 것이다.

```
@Service
public class MessageService {

    @Async
    public void print(String message) {
        System.out.println(message);
    }
}

@RequiredArgsConstructor
@RestController
public class MessageController {

    private final MessageService messageService;

    @GetMapping("/messages")
    @ResponseStatus(code = HttpStatus.OK)
    public void printMessage() {
        for (int i = 1; i <= 100; i++) {
            messageService.print(i + "");
        }
    }
}
```

이 방식은 추천하지 않는데, 스레드를 관리하지 않기 때문이다. @Async의 기본 설정은 SimpleAsyncTaskExecutor를 사용하도록 되어 있는데, 이는 스레드 풀이 아니 단순 스레드를 만드는 역할을 한다. 


## Spring @Async with ThreadPool

Spring에서 스레드풀을 사용하기 위해, 우선 Application 클래스에서 @EnableAsync를 제거한다. Application 클래스에 @EnableAutoConfiguration 또는 @SpringBootApplication이 설정되어 있다면, 런타임 시 @Configuartion이 설정된 Config클래스의 threadPoolTaskExecutor 빈 정보를 읽어들이기 때문이다. 

```
@Configuartion
@EnableAsync
public class SpringAsyncConfig {

    @Bean(name="threadPoolTaskExecutor")
    public Executor threadPoolTaskExecutor() {
    
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        taskExecutor.setCorePoolSize(3);
        taskExecutor.setMaxPoolSize(30);
        taskExecutor.setQueueCapacity(100);
        taskExecutor.setThreadNamePrefix("YURIM_SPRING_THREAD_EXECUTOR");
        return taskExecutor;
   
    }
}

``` 

* CorePoolSize : 기본 스레드 수
* MaxPoolSize : 최대 스레드 수
* QueueCapacity : Queue size
* ThreadNamePrefix : 스레드 이름 앞에 명시할 수 있는 옵션


최초 core 사이즈만큼 동작하다가 이 이상 작업을 처리할 수 없을 경우 max 사이즈만큼 스레드가 증가하는 것이 아니다. 내부적으로 Integer.MAX_VALUE 사이즈의(QueueCapacity가 설정되어 있다면 해당 개수만큼) LinkedBlockingQueue를 생성해서 core 사이즈만큼의 스레드에서 작업을 처리 할 수 없을 경우 Queue에서 대기하게 된다. Queue가 꽉 차게 되면 그때 max 사이즈만큼 스레드를 생성해서 처리하게 된다.    
위의 예시로 들자면 최초 3개의 스레드에서 처리하다가 처리 속도가 밀릴 경우 작업을 100개 사이즈 Queue에 넣어놓고 그 이상의 요청이 들어오면 최대 30개의 스레드를 생성해서 작업을 처리하게 된다. 


```
@Service
public class MessageService {

    @Async("threadPoolTaskExecutor")
    public void print(String message) {
        System.out.println(message);
    }
}
```

스레드 풀 설정을 적용하고 싶은 메소드에 @Async("설정한 빈이름")으로 명시해 주면 된다. 스레드 풀 설정 빈은 메소드마다 다른 설정값을 적용하고 싶다면, 빈을 여러개 만들어 관리할 수 있다. 

+)

```
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.AsyncConfigurerSupport;
import org.springframework.scheduling.annotation.EnableAsync;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

@Configuration
@EnableAsync
public class SpringAsyncConfig extends AsyncConfigurerSupport {
    
    @Override
    public Executor getAsyncExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(30);
        executor.setQueueCapacity(50);
        executor.setThreadNamePrefix("DDAJA-ASYNC-");
        executor.setThreadNamePrefix("YURIM_SPRING_THREAD_EXECUTOR");
        return executor;
    }
}
```
Config class는 AsyncConfigurerSupport 를 상속받아 구현할 수도 있다.



### RejectedExecutionHandler

max 스레드까지 생성하고 queue까지 꽉 찬 상태에서 추가 요청이 오면 RejectedExecutionException 예외가 발생한다. 더 이상 처리할 수 없다는 오류인데, 이 오류를 핸들링하기 위해 RejectedExecutionHandler 인터페이스를 구현한 몇가지 클래스가 제공된다. 

* AbortPolicy
    * 기본설정
    * RejectedExecutionException 발생

* DiscardOldestPolicy
    * 오래된 작업을 skip
    * 모든 task가 무조건 처리되어야 할 필요가 없을 경우 사용

* DiscardPolicy
    * 처리하려는 작업을 skip
    * 모든 task가 무조건 처리되어야 할 필요가 없을 경우 사용

* CallerRunsPolicy
    * shutdown 상태가 아니라면 ThreadPoolTaskExecutor에 요청한 thread에서 직접 처리
    * 예외와 누락없이 최대한 처리하려면 해당 옵션 사용


```
@Bean(name="threadPoolTaskExecutor")
    public Executor threadPoolTaskExecutor() {
    
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        taskExecutor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        return taskExecutor;
   
    }

```

### Shutdown 처리

```
POST http://localhost:8888/actuator/shutdown
```
별도로 정의한 스레드 풀에서 작업이 이루어지고 있을때 애플리케이션 종료 요청을 하게 되면 아직 처리되지 못한 task는 유실된다. 유실 없이 마지막까지 다 처리하고 종료되길 원한다면 설정을 추가해줘야 한다. 
종료는 Spring Boot Actuator를 이용할 수 있다. 


```
@Bean(name="threadPoolTaskExecutor")
    public Executor threadPoolTaskExecutor() {
    
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        taskExecutor.setWaitForTasksToCompleteOnShutdown(true);
        taskExecutor.setAwaitTerminationSeconds(60); //shutdown 최대 60초 
        return taskExecutor;
   
    }

```
* WaitForTasksToCompleteOnShutdown : true로 하면 queue에 남아있는 모든 작업이 완료될 때까지 기다림
* AwaitTerminationSeconds : shutdown 최대 대시간 설정


## 주의사항

@Async 어노테이션을 사용할때 세 가지 사항을 주의하자.

**1. private method 사용불가, public method만 사용가능**.  
**2. self-invocation(자가 호출), 즉 inner method는 사용 불가**.  
**3. QueueCapacity 초과 요청에 대한 비동기 method 호출시 방어코드 작성**.  

자세한 이유는 [Spring-Async-Annotation비동기-메소드-사용하기](https://velog.io/@gillog/Spring-Async-Annotation%EB%B9%84%EB%8F%99%EA%B8%B0-%EB%A9%94%EC%86%8C%EB%93%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)에서 확인하자.



   
출처:
https://steady-coding.tistory.com/611  
https://kapentaz.github.io/spring/Spring-ThreadPoolTaskExecutor-%EC%84%A4%EC%A0%95/#   
http://dveamer.github.io/java/SpringAsync.html   
https://velog.io/@gillog/Spring-Async-Annotation%EB%B9%84%EB%8F%99%EA%B8%B0-%EB%A9%94%EC%86%8C%EB%93%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0   
