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

