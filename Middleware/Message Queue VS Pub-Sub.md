
채팅서버를 개발하게 되었는데, 이 때 시스템 구조 상에서 redis를 message queue 로 쓰기도 하고 pub-sub로 쓰기도 한다고 한다. 
비슷한 개념이라 헷갈려서 이참에 정리를 해보았다.


## Asynchronous Messaging Pattern

비동기 메시징은 메세지를 전달함에 있어 **발행자(producer)와 구독자(consumer)를 분리**시킴으로써 확정성, 안정성, 성능 향상을 목적으로 적용된 패턴이다. 
크게 **Message Queue 와 Publish-Subscribe 2가지로 나뉜다.**


## Message Queue

### 구성

![image](https://user-images.githubusercontent.com/45115557/197784009-ad84023f-4bde-4c89-997c-ecbe1c373b78.png)


* 메세지를 만드는 발행자(publisher)
* 메시지를 보관하는 큐(Queue)
* 메시지를 소비하는 여러개의 소비자(consumer)















참고링크:   
https://escapefromcoding.tistory.com/706
