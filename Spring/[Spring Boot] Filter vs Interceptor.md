## Spring 흐름

![spring](https://user-images.githubusercontent.com/45115557/194739307-58d96f72-2c13-41dc-b6bc-4e696f4072fa.PNG)


## 필터 (Filter)


HTTP 요청이 오면 Dispatcher Servlet에 요청을 전달하기 전/후에 filter 처리를 한다. Dispatcher Servlet은 스프링의 가장 앞단에 존재하는 프론트 컨트롤러이므로, 필터는 스프링 범위 밖에서 처리가 된다. 즉, **스프링 컨테이너가 아닌 톰캣과 같은 웹 컨테이너에 의해 관리가 된다. (스프링 빈으로 등록은 된다)**

```java
public interface Filter { 
	public default void init(FilterConfig filterConfig) throws ServletException {} 
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException; 
	public default void destroy() {} 
}
```

doFilter method를 통해 url-pattern에 맞는 모든 HTTP 요청에 대한 처리를 할 수 있다.  

### 용도

- 공통 인증/인가 작업
- 모든 요청에 대한 로깅/감사
- Spring 과 분리되어야 하는 기능
- ServletRequest, ServletResponse를 조작하고 싶을 때 (인터셉터는 불가능)

#### 에러처리
에러 발생 시 Web Application에서 실행해야 하기 때문에 tomcat을 사용한다면 `<error-page>`를 선언하거나 Filter내에서 에러를 잡아 `request.getRequestDespatcher(String)` 으로 다뤄야 한다. 


### Custom Filter
```
public class SomeFilter implements Filter {
  //...
  
  public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
    chain.doFilter(new BodyCachedServletRequestWrapper(request), response);
  }
}

```
직접 정의한 필터를 사용하고 싶다면 다음과 같이 web의 Filter를 구현하여 사용할 수 있다. 
Filter는 GenericFilterBean, OncePerRequestFilter 등 목적에 따라 Filter를 확장한 인터페이스를 구현하면 된다. 


# 인터셉터(Interceptor)

필터와 달리 스프링이 제공하는 기술이다. DispatcherServlet이 컨트롤러를 호출하기 전에 요청, 응답을 참조하거나 가공한다. 


```java
public interface HandlerInterceptor { 

	//컨트롤러 호출 전 실행. 컨트롤러 이전 처리해야 하는 작업이나 요청 정보를 가공
	//반환값이 false이면 이후 과정은 진행x
	default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception { return true; } 

  //컨트롤러 호출 후 실행. RestController에는 잘 사용하지 않음
	default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception { } 
	
	// view 렌더링 후 실행
	default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception { } 
}

```

### 용도

- 세부적인 보안, 인증/인가 작업
- API 호출에 대한 로깅 또는 감사
- Controller로 넘겨주는 정보의 가공

#### 에러처리
에러 발생 시 Interceptor는 Spring의 ServletDispatcher 내에 있기 때문에 `@ControllerAdvice`에서 `@ExceptionHandler`를 사용해서 예외처리를 할 수 있다. 작성해야 할 전후처리 로직에서 예외를 전역적으로 처리하고 싶다면 Interceptor가 좀 더 적합니다. 


## CF..

보통 애플리케이션 전역에 영향을 주는 작업은 Filter, 특정 영역에 해당하는 작업은 Interceptor로 처리한다고 하나 Filter도 Interceptor도 모두 요청에 대한 전후처리 역할을 수행한다. 또 Filter도 Uri를 기반으로 언제 실행할 것인지 조정한다.
오히려 둘의 차이점은 에러처리가 더 명확하다. 

추가로 Filter도 이제는 Spring Bean 등록이 가능한데 이는 망나니 개발자님의 [[Spring] 필터(Filter)가 스프링 빈 등록과 주입이 가능한 이유(DelegatingFilterProxy의 등장)](https://mangkyu.tistory.com/221) 을 참고하자.

<br/><br/><br/>

참고링크: 
https://mangkyu.tistory.com/173   
https://supawer0728.github.io/2018/04/04/spring-filter-interceptor/   
https://goddaehee.tistory.com/154
