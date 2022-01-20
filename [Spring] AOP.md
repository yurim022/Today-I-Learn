프로젝트를 진행하면서 공통로깅을 적용해야 하는 요구사항이 있었다. 
AOP를 통해 구현하고자 AOP에 대해 공부해보았다.


## AOP란

Aspect oriented Programming의 약자로 관점 지향 프로그래밍 이라 불린다. 즉, 어떤 로직을 기준으로 핵심적인관점-부과적인 관점으로 나누어서 보고 그 관점을 기준으로 각각 모듈화하겠다는 것이다. 

![image](https://user-images.githubusercontent.com/45115557/150278737-c611b8fa-5523-4c39-9791-4f8811b46d32.png)
위와 같이 흩어진 관심사를 Aspect로 모듈화하고 핵심적인 비즈니스 로직에서 분리하여 재사용하겠다는 것이 AOP이다. 

## 주요개념
- Aspect: 흩어진 관심사를 모듈화 한 것, 주로 부가기능을 모듈화함(로깅 등)
- Target: Aspect를 적용하는 곳(클래스, 메서드..)
- Advice: 실질적으로 어떤 일을 해야할지에 대한 것,실질적인 부가기능을 담은 구현체
- JointPoint: Advice가 적용될 위치. 끼어들 수 있는 지점, 메서드 진입 지점, 생성자 호출 시점, 필드에서 값을 꺼내올때 등 다양한 시점에 적용가능.
- PointCut: JointPoint의 상세한 스펙을 정의한 것. 'A란 메서드의 진입 시점에 호출할 것'과 같이 더욱 구체적으로 Advice가 실행될 지점을 정할 수 있다. 

## 스프링 AOP 특징
- 프록시 패턴 기반의 AOP구현체 (프록시 객체를 쓰는 이유는 접근 제어 및 부가기능을 추가하기 위해서)
- 스프링 빈에만 AOP 적용 가능
- 모든 AOP기능을 제공하는 것이 아닌 스프링 IoC와 연동하여 중복코드, 프록시 클래스 작성의 번거로움, 객체들 간 관계 복잡도 증가 등에 대한 해결책을 지원하는 것이다.

## @Aspect, @Around

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```
의존성을 먼저 추가해준다. 


```
@Component
@Aspect
public class PerfAspect {

@Around("execution(* com.saelobi..*.EventService.*(..))")
public Object logPerf(ProceedingJoinPoint pjp) throws Throwable{
  long begin = System.currentTimeMillis();
  Object retVal = pjp.proceed(); // 메서드 호출 자체를 감쌈
  System.out.println(System.currentTimeMillis() - begin);
  return retVal;
  }
}

```

@Aspect 어노테이션을 붙여 이 클래스가 Aspect를 나타내는 클래스라는 것을 명시하고 @Component를 붙여 스프링 빈으로 등록한다. 
@Around 어노테이션은 타켓 메서드를 감싸서 특정 Advice를 실행한다는 의미이다. 
execution(* com.saelobi..*.EventService.*(..))가 의미하는 바는 com.saelobi 아래의 패키지 경로의 EventService 객체의 모든 메서드에 이 Aspect을 적용하겠다는 의미이다. 


```
@Around("@annotation(PerLogging)")
```

경로지정 외에 특정 어노테이션이 붙은 포인트에 해당 Aspect을 실행할 수 있는 기능도 제공한다. 


```
@Around("bean(simpleEventService)")
```

스프링 빈의 모든 메서드에 적용할 수도 있다. 



## @Around 이외에 타겟 메서드에 실행지점을 지정하는 어노테이션

- @Before (이전) : 어드바이스 타겟 메소드가 호출되기 전에 어드바이스 기능을 수행
- @After (이후) : 타겟 메소드의 결과에 관계없이(즉 성공, 예외 관계없이) 타겟 메소드가 완료 되면 어드바이스 기능을 수행
- @AfterReturning (정상적 반환 이후)타겟 메소드가 성공적으로 결과값을 반환 후에 어드바이스 기능을 수행
- @AfterThrowing (예외 발생 이후) : 타겟 메소드가 수행 중 예외를 던지게 되면 어드바이스 기능을 수행
- @Around (메소드 실행 전후) : 어드바이스가 타겟 메소드를 감싸서 타겟 메소드 호출전과 후에 어드바이스 기능을 수행




참고 링크:
https://engkimbs.tistory.com/746
