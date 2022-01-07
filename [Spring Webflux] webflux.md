## Webflux 란

+ Spring5에서 새롭게 추가된 모듈
+ SpringBoot2 부터 도입
+ java8부터 지원
+ 클라이언트, 서버에서 reactive 스타일의 어플리케이션 개발을 도와주는 모듈 
+ non-blocking
+ reactive stream을 지원

### 장점
![image](https://user-images.githubusercontent.com/45115557/148479505-e5eb87ae-5922-40ca-9f98-aabe153197c0.png)

+ 고성능 (비동기 처리이므로 기다릴 thread가 응답을 기다리지 않고 클라이언트의 다른 요청을 받을 수 있음)
+ netty 지원
+ 비동기 non-blocking 메세지 처리

### 단점
+ 오류처리가 복잡
+ Back Pressure 기능 없음

## Spring Boot2 Stack
![image](https://user-images.githubusercontent.com/45115557/148479076-209b7460-087d-4c01-a527-e18cd0b4a465.png)

크게 Reactive Stack 과 Servlet Stack이 있다. Servlet Stack에서는 동기처리가 필요한 JDBC, JPA와 더불어 NoSQL 을 지원하며 Reactive Stack에서는 대부분 NoSQL 형태를 지원한다. 관계형은 비동기 처리 모델과 잘 맞지 않는다고 한다.




## Spring MVC vs WebFlux

![image](https://user-images.githubusercontent.com/45115557/148479579-4d2feae5-35f0-4f72-ae8e-5c2716771367.png)

둘다 @Controller, Reactive clients 이며 공통적으노 Tomcat, Jetty, Undertow와 같은 서버에서 실행할 수 있다. 
Spring MVC 에서는 명령형 논리, JDBC, JPA를 가질 수 있으며 Spring WebFlux에서는 기능적 엔드포인트, 이벤트 루프, 동시성 모델을 가질 수 있다. Netty 서버에서 실행할 수 있다는 장점이 있다. 

#### Netty 서버란?

![image](https://user-images.githubusercontent.com/45115557/148480313-5c3e5b1b-6e1e-4446-b489-b429c4db0941.png)

기존의 소켓 프로그래밍은 클라이언트 요청당 thread가 할당되어(1:1) 많은 클라이언트 접속 시 리소스 낭비와 문맥 교환 이슈, 입출력 데이터의 무한 대기 현상 이슈가 있었다. 
이러한 네트워크 문제를 해결하기 위해 NIO(Non-Blocking Input Output)이 개발되었다.

java.nio.channels.Selector에서 입출력을 하고 있는 Socket의 집합 상태를 확인하기 위해 이벤트 통지API를 사용하기 때문에 클라이언트 당 스레드를 생성하지 않고, 필요할 때마다 스레드에게 통지를 하기 때문에 비동기적인 통신 구조를 갖출 수 있게 되었다. 
**즉, 적은 수의 스레드로 더 많은 Connection을 취할 수 있어서 메모리 관리에 이점이 생기고 컨텍스트 스위치에 대한 오버헤드가 줄어들게 된다. **





참고링크:
https://devuna.tistory.com/108
https://narup.tistory.com/118
