## @Configuration vs @Bean

**한줄로 요약하자면, @Configuration은 싱글톤으로 클래스를 유지해주고 @Configuration이 붙은 클래스의 하위에 정의된 @Bean을 해당 클래스를 상속받아 등록하도록 만들어준다.**


```
package Test.sample;

import Test.sample.calculate.Calculate;
import Test.sample.calculate.Calculator;
import Test.sample.calculate.Minus;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public Calculate calculate(){
        System.out.println("AppConfig.calculate");
        return new Minus();
    }

    @Bean
    public Calculator calculator(){
        System.out.println("AppConfig.calculator");
        return new Calculator(calculate());
    }

}
```



```
package Test.sample.configuration;

import Test.sample.AppConfig;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

class AppConfigTest {

    /*
     @Configuration 사용했을경우
     */
    @Test
    void configurationTest(){
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
        AppConfig appConfig = ac.getBean(AppConfig.class);

        System.out.println(appConfig);
    }

}
```
다음 테스트 코드를 출력시, 3개가 출력될 것 같지만 AppConfig.calculate , AppConfig.calculator 두가지만 출력된다. 
@Configuration을 빼고 출력하면 3개가 출력된다.


참고링크: https://castleone.tistory.com/2
