# Spring Websocket

회사에서 채팅서버를 개발하고 있다. 에러코드 정의와 글로벌 에러처리를 진행하면서 웹소켓의 에러처리를 하게 되었는데, 이 김에 웹소켓에 대한 정리를 좀 해보고자 한다. 

먼저 [여기](https://supawer0728.github.io/2018/03/30/spring-websocket/) 에 정리가 아주 잘 되어있으니 참고하실 분은 여기를..!!

### Websocket이란

websocket이란 양방향 통신이다. HTTP와 같이 stateless 하지 않고 `Full Duplex, 2-way communication` 방식이다. 
