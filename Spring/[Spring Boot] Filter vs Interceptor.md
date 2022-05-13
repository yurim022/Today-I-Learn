## 필터 (Filter)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/397e5bb3-1966-4ed6-a49e-94b39cd54144/Untitled.png)

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

# 인터셉터(Interceptor)

필터와 달리 스프링이 제공하는 기술이다. DispatcherServlet이 컨트롤러를 호출하기 전에 요청, 응답을 참조하거나 가공한다. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/152c8b19-9df6-4fcb-bf74-49941d8d68db/Untitled.png)

```java
public interface HandlerInterceptor { 

	//컨트롤러 호출 전 실행. 컨트롤러 이전 처리해야 하는 작업이나 요청 정보를 가공
	//반환값이 false이면 이후 과정은 진행x
	default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception { return true; } 

  //컨트롤러 호출 후 실행. RestController에는 잘 사용하지 않음
	default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception { } 
	
	//
	default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception { } 
}

```

### 용도

- 세부적인 보안, 인증/인가 작업
- API 호출에 대한 로깅 또는 감사
- Controller로 넘겨주는 정보의 가공
