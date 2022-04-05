프로젝트 코드를 분석하던 중 SpringBootServletInitializer 를 상속받는 ServletInitializer 클래스를 발견했다.

```
public class ServletInitializer extends SpringBootServletInitializer {

	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
		return application.sources(Application.class);
	}

}
```

뭐하는 녀석일까?

### 용도

스프링부트 웹 어플리케이션을 배포할 때 jar 파일로도 배포 할 수 있지만 전통적인 war 파일로 배포할 때, SpringBootServeltInitializer 를 사용한다. 

스프링 웹 어플리케이션이 tomcat에서 동작되기 위해선 web.xml에 application context를 등록해야 한다.
servlet 3.0 부터 web.xml 설정을 WebApplicationInitializer 인터페이스를 구현하여 대신 할 수 있게 되었고, 이를 구현한 SpringBootServletInitializer를 상속받아 외부 Tomcat에서 스프링부트가 실행되도록 해준다.

