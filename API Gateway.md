# API Gateway 의 필요성

API Gateway는 서버 앞단에서 모든 API 서버들의 엔드포인트를 단일화 해주는 또다른 서버이다. API에 대한 인증과 인가 기능을 가지며, 메세지의 내용에 따라 어플리케이션 내부에 있는 마이크로 서비스로 라우팅해준다. 

주로 MSA 아키텍처에서 사용하고 있는데, 클라이언트에서 잘게 쪼개어진 여러개의 서비스를 각각 호출하다보면 다음과 같은 문제점이 발생한다. 
+ 각각의 서비스마다 인증/인가 등 공통된 로직을 구현해야 한다. 
+ 수많은 API 호출을 기록하고 관리하는 것이 어렵다. 
+ 내부의 비즈니스 로직이 드러나게 되어 보안에 취약하다. 

![image](https://user-images.githubusercontent.com/45115557/147713595-1bb0ca91-cd47-4bc1-bd1b-12433b2dc262.png)

















참고 링크:
https://velog.io/@tedigom/MSA-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-3API-Gateway-nvk2kf0zbj
