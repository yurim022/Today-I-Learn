# Microservice 장애처리

[인프런 MSA 강의](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C-%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C%EC%84%9C%EB%B9%84%EC%8A%A4)
를 보고 학습한 내용이다. 

</br>

```
serviceA -> 호출 -> serviceB
```

msa 아키텍처의 경우, serviceA 가 serviceB를 호출할 때 오류가 발생하면 serviceB의 오류가 serviceA에도 전파되는 문제점이 있다. 
serviceA의 문제가 아님에도 serviceA의 문제처럼 보일 수 있는데, 장애가 전파되는 것을 막기 위해 serviceA에서는 serviceB에서 오류가 발생해도 문제가 없었던 것처럼 정상적인 데이터를 보여줄 수 있게끔 준비해야 한다. 

</br>

## Circuit Breaker란

장애 전파를 막기 위해 사용하는 것이 바로 circuit breaker 이다. 

```
serviceA -> Circuit Breaker -> serviceB
```

기본적으로 두 서비스간 모든 호출은 Circuit Breaker을 통하게 되고, 서비스가 정상적인 상황에는 트래픽이 문제없이 통과한다.    
반면 serviceB에서 문제를 감지했다면 serviceB로의 호출을 강제로 끊어서 serviceA의 쓰레드들이 더이상 요청에 대한 응답을 기다리지 않도록 장애가 전파되는 것을 방지한다.    
하지만 서킷브레이커가 강제로 호출을 끊는 방식을 채택하고 있기 때문에 serviceA는 이러한 경우에 대한 장애처리 로직이 구현되어야 한다. 이때 circuit breaker에서는 정해진 룰에 따라 임시방편이 될만한 메세지를 리턴할 수 있는데 이를  `fall back 메시징`이라 일컫는다. 

![image](https://user-images.githubusercontent.com/45115557/200856565-23f3d796-6088-450e-ba7a-0b87318b3838.png)

즉 정리하자면 circuit breaker의 역할은 크게 두 가지 이다. 

* **장애가 발생하는 서비스에 반복적인 호출이 되지 못하게 차단**
* **특정 서비스가 정상적으로 동작하지 않을 경우 다른 기능으로 대체 수행(fall back message) -> 장애 회피** 

</br>

## Close VS Open

![image](https://user-images.githubusercontent.com/45115557/200859651-61ec97f2-786a-4e68-b69b-b85d3318eb38.png)


### Close

circuit breaker가 닫혔다는 것은 작동하지 않았다는 뜻으로, **정상적으로 다른 마이크로 서비스를 사용할 수 있는 상태**이다. 

### Open

**circuit breaker가 작동하는 상태**이다. serviceB 에 접속이 안되었다던가, 다른 이유에 의해 정상적인 서비스가 불가능하고 일정한 수치에 다다랐을때 서킷브레이커가 오픈상태가 된다.   
*ex.    
30초 안에 10번의 호출을 했는데 절반 이상의 수치동안 실패.     
70% 이상의 확률로 원하는 데이터가 돌아오지 않음 등,,*   

서킷브레이커가 open이 되었다는 것은 클라이언트의 요청을 더이상 최종적인 마이크로 서비스에게 전달하지 않고 circuit breaker 자체적으로 우회하는 값을 리턴시켜주는 작업을 의미한다. 


</br>

## Resilience4j

circuit breaker를 적용하기 위한 라이브러리에는 Resilience4j이 있다. 

pom.xml에 다음과 같이 의존성을 추가해준다. 

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
</dependency>
```

user서비스에서 order서비스를 호출하는 관계라 하자. order service에 문제가 생겼을때 fallback 하기 위한 코드이다. 

userSerive.java
```java
@Autowired
CircuitBreakerFactory circuitBreakerFactory;

CircuitBreaker circuitBreaker = circuitBreakerFactory.create("circuitbreaker");
List<ResponseOrder> orderList = circuitBreaker.run(() -> orderServiceClient.getOrders(userId), throwable -> new ArrayList());

userDto.setOrders(orderList);
```

이렇게 구현하면 circuitbreaker가 open되었을 때 빈 리스트를 반환해준다. 

</br>


## CircuitBreaker Customize

기본으로 제공되는 CircuitBreaker가 아닌 customize 해서 사용하고 싶다면 Resilience4JCircuitBreakerFactory를 Customizer로 감싸서 사용하면 된다. 

```java
@Configuration
public class Resilience4JConfiguration {

    @Bean
    public Customizer<Resilience4JCircuitBreakerFactory> globalCustomConfiguartion() {

        CircuitBreakerConfig circuitBreakerConfig = CircuitBreakerConfig.custom()
            .failureRateThreshold(4);
            .waitDurationInOpenState(Duration.ofMillis(1000)) //1sec
            .slidingWindowType(CircuitBreakerConfig.SlidingWindowType.COUNT_BASED)
            .slidingWindowSize(2)
            .build();
    }
}
```

* **failureRateThreshold** : CircuitBreaker를 open할지 결정하는 failure rate threshold percentage. default 5 (50%)
* **waitDurationInOpenState** : CircuitBreaker를 open한 상태를 유지하는 지속 시간. 이 기간 이후에는 half open 상태이다. default 60 seconds
* **slidingWindowType** : CircuitBreaker가 닫힐때 통화 결과를 기록하는데 사용되는 슬라이딩 창의 유형. COUNT based OR TIME based
* **slidingWindowSize** : CircuitBreaker가 정상작동이라 판단하여 close 될때 호출결과를 기록하는데 사용되는 슬라이딩 창의 크기. default 100

</br>

참고링크:
https://godekdls.github.io/Resilience4j/circuitbreaker/   
https://velog.io/@hyeondev/MSA-%EC%84%9C%EB%B9%84%EC%8A%A4%EC%97%90%EC%84%9C-Circuit-Breaker-%EB%8F%84%EC%9E%85%ED%95%98%EA%B8%B0   

