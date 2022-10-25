
채팅서버를 개발하게 되었는데, 이 때 시스템 구조 상에서 redis를 message queue 로 쓰기도 하고 pub-sub로 쓰기도 한다고 한다. 
비슷한 개념이라 헷갈려서 이참에 정리를 해보았다.


## Asynchronous Messaging Pattern

비동기 메시징은 메세지를 전달함에 있어 **발행자(producer)와 구독자(consumer)를 분리**시킴으로써 확장성, 안정성, 성능 향상을 목적으로 적용된 패턴이다. 
크게 **Message Queue 와 Publish-Subscribe 2가지로 나뉜다.**

</br>

## Message Queue

### 구성

![image](https://user-images.githubusercontent.com/45115557/197784009-ad84023f-4bde-4c89-997c-ecbe1c373b78.png)


* 메세지를 만드는 발행자(publisher)
* 메시지를 보관하는 큐(Queue)
* 메시지를 소비하는 여러개의 소비자(consumer)

</br>

### 동작 방식

1. publish 서비스가 메세지를 큐 혹은 exchange에 넣는다.
2. consumer가 메세지를 소비한다. 

![image](https://user-images.githubusercontent.com/45115557/197784785-d0fba5ff-88d6-42fd-9791-af87211d867d.png)

이 때, 여러개의 consumer가 메세지를 읽고 있다면 한번 소비된 메세지는 다른 consumer에서는 소비할 수 없다.    
예를 들어 위의 그림과 같이 consumerA가 m1 메세지를 읽었다면 consumerB는 m1 메세지를 읽을 수 없다. 
이러한 구조는 **메세지 처리 작업을 소비자에게 위임하는 형태로써, 작업이 한 번만 실행되도록** 할 수 있다. 이러한 점을 잘 적용할 수 있는 예를 알아보자. 

</br>

### 적용 예시

Message Queue는 MSA환경에서 많이 사용된다.

**클라우드 기반 혹은 서버리스 분산처리 환경**에서 같은 큐를 listen하는 여러개 소비자를 추가하여 작업 속도를 높일 수 있고, 트래픽이 적을 때는 비용을 절약하기 위해 소비자를 종료시키며 **부하에 따라 앱 Scaling**을 할 수 있다. 

</br>












참고링크:   
https://escapefromcoding.tistory.com/706
