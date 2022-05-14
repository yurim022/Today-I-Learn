## Custom Annotation 

Spring annotation을 custom 하게 만들 수 있는 기능을 제공하는데, 왜 사용하는건지 궁금했다. 

> 커스텀 어노테이션은 **정보전달 에 유리하다.**
> 

기본정인 구조에 대해 알아보자. 

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface LogData {

	EventType event();
}
```

대부분은 RUNTIME Retention이라고 볼 수 있고, 좀 다른 점이 Target이라 할 수 있다.

- @Target(ElementType.Type) → class
- @Target(ElementType.FIELD) → 클래스 내 필드
- @Target(ElementType.METHOD) → 메소드

어노테이션을 적용할 범위를 지정할 수 있다. 

만약 로깅을 위해서 메소드 단위로 어노테이션을 적용한다면, 

```java
LoginController{
	@PostMapping
	@LogData(event = EventType.LOGIN)
	public void login(@RequestBody login, HttpServletRequest request){
	
		loginService.login(login);
	}
}
```

이런식으로 컨트롤러 내 메소드에 어노테이션을 적용해서 

```java
@Slf4j
public class LogInterceptor implements HandlerInterceptor {
	@Override
	public void afterCompletion(...생략){
			LogData logInfo = handlerMethod.getMethodAnnotation(LogData.class); 
			log.info(logInfo.event().event);
	}
}
```

와 같이 로그 정보를 가져와 남길 수 있다. 

참고링크:

[https://www.baeldung.com/java-custom-annotation](https://www.baeldung.com/java-custom-annotation)
