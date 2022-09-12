# WEB VS WAS 

### WEB
* Apachi
* NginX
* 정적데이터

Apachi는 멀티 프로세스 모듈(MPM) 방식으로 한 클라이언트당 하나의 쓰레드가 배정되는 방식이고, NginX는 이벤트(Event-Driven) 방식으로 비동기로 처리된다. 



### WAS
* Tomcat (+SpringMVC)
* Netty  (+SpringWeflux)
* 동적데이터 (DB 접근)

cf)
Node.js는 Tomcat,Spring 뒤에 개발되었기 때문에 올인원으로 관리자가 내장되어 있는 구조라고 보면 된다. 즉 Node.js = Tomcat + Spring


## Node.js vs SpringBoot

### Node.js
* Non-blocking I/O 를 처리하는데 최적화
* Single Thread 이므로 이벤트가 아무리 많아도 메모리 사용량과 시스템 리소스 사용량에 변화가 거의 없다
* 쓰레드 하나가 무너지면 프로그램 전체가 무너진다
* 싱글쓰레드 이므로 하나의 작업자체가 오래 걸리는 웹서비스에는 어울리지 않는다 (반대로 가벼운 I/O가 많은 게시판, 채팅, 스트리밍과 같은 웹서비스에 적하바다
* 서비스 로직이 복잡해지거나 업무난이도가 높은 경우 Type Safe 하지 못한 JS의 특징으로 인해 Type으로 인한 런타임 에러를 직면하게 될 수 있고, 리팩토링 및 확장이 어렵다

### SpringBoot
* 언어의 역사가 길어 안정적이다
* 리팩토링과 확장에 용이하며, IDE의 지원을 받을 수 있다
* 단위가 큰 프로젝트에 어울리며, 멀티쓰레드로 운용하기 때문에 단일작업의 수행시간이 오래걸려도 서비스 자체가 죽지 않는다
* 멀티쓰레드 이기 때문에, 메모리 사용량이 Node.js보다 많다



참고링크:   
https://ooeunz.tistory.com/109   
https://developer-ping9.tistory.com/245   
https://developer-ping9.tistory.com/197   
