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

전체 스레드의 개수를 10개로 제한하고 있고, 멀티 스레딩 방식의 비동기 처리도 할 수 인ㅆ게 되었다. 하지만 위 방식은 비동기 방식으로 처리하고 싶은 메소드마다 ExecutorService의 submit() 메소드를 적용해야 하므로 반복적인 수정작업이 필요하다. 처음에 동기로 작성한 메소드를 비동기로 바꾸고 싶다면 메소드 자체의 로직을 변경해야 하는 불편함이 있다. 


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

먼저 첫번째 방식은 @EnableAsync 어노테이션을 Application 클래스에 붙여주고, 비동기 방식으로 처리하고 싶응ㄴ 동기 로직의 메소드 위에 @Async를 붙이는 것이다.

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

    @Bearn(name="threadPoolTaskExecutor")
    publi Executor threadPoolTaskExecutor() {
    
        ThreadPoolTaskExecutor taskExecutor = new ThreadPooltaskExecutor();
        taskExecutor.setCorePoolSize(3);
        taskExecutor.setMaxPoolSize(30);
        taskExecutor.setQueueCapacity(100);
        taskExecutor.setThreadNamePrefix("YURIMS_Spring_Thread_Executor");
        return taskExecutor;
   
    }
}

```



출처:
https://steady-coding.tistory.com/611   
http://dveamer.github.io/java/SpringAsync.html   
https://velog.io/@gillog/Spring-Async-Annotation%EB%B9%84%EB%8F%99%EA%B8%B0-%EB%A9%94%EC%86%8C%EB%93%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0   
