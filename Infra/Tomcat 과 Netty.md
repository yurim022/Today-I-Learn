## WEB VS WAS 

### WEB
* Apachi
* NginX
* 정적데이터

Apachi는 멀티 프로세스 모듈(MPM) 방식으로 한 클라이언트당 하나의 쓰레드가 배정되는 방식이고, NginX는 이벤트(Event-Driven) 방식으로 비동기로 처리된다. 



### WAS
* Tomcat (+SpringMVC)
* Netty  (+SpringWeflux)
* Node.js 
* 동적데이터 (DB 접근)

Node.js는 Tomcat,Spring 뒤에 개발되었기 때문에 올인원으로 관리자가 내장되어 있는 구조라고 보면 된다. 즉 Node.js = Tomcat + Spring




참고링크:
https://ooeunz.tistory.com/109
https://developer-ping9.tistory.com/245
https://developer-ping9.tistory.com/197
