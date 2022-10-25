
채팅서버를 개발하게 되었는데, 이 때 시스템 구조 상에서 redis를 message queue 로 쓰기도 하고 pub-sub로 쓰기도 한다고 한다. 
비슷한 개념이라 헷갈려서 이참에 정리를 해보았다.


## Asynchronous Messaging Pattern

비동기 메시징은 메세지를 전달함에 있어 **발행자(producer)와 구독자(consumer)를 분리**시킴으로써 확장성, 안정성, 성능 향상을 목적으로 적용된 패턴이다. 
크게 **Message Queue 와 Publish-Subscribe 2가지로 나뉜다.**


</br>

# Message Queue

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


# Publish - Subscribe

### 구성

![image](https://user-images.githubusercontent.com/45115557/197793457-7e33d2b5-15b3-47da-bd3e-816c48bb7f85.png)

* publisher
* Topic
* Queue
* Subscriber

</br>

### 동작 방식 

![image](https://user-images.githubusercontent.com/45115557/197794664-eeed5d05-e784-44ca-a511-a105a6e40dd1.png)

1. Publisher는 Topic에 메세지를 보낸다. 
2. Topic을 구독하고 있는 Subscriber에게 브로드캐스트로 전송한다.

Pub-Sub 구조에서는 메세지들은 큐에 보내지고, 각 큐는 메세지를 listen하고 있는 구독자 서비스를 가지고 있다.
즉 **모든 구독자(subscriber)는 발행자(publisher)가 exchange에 전송하는 메세지 복사본을 1개 이상 가진다.** 

#### 구독 방식

* **ephemeral subscription(일시적 구독)**

구독은 consumer가 구동중일때만 가능하다. consumer가 다운된다면 구독과 아직 처리되지 않은 메세지는 유실된다. 

* **durable subscription(지속적 구독)**

구독은 명시적으로 제거되지 않는다면 계속 유지된다. consumer가 다운되면 메세징 플랫폼은 구독을 유지시키고, 나중에 다시 메세징 처리가 가능하다. 


## 두 방식 비교

### 공통점

* 수평확장에 용이
* 전통적인 동기 방식보다 지속성(durability)가 좋음

예를 들어 앱A가 앱B에 비동기 통신을 보냈을때 앱이 다운되었다면 데이터는 유실되고 다시 요청을 보내야 한다. 
Message Queue를 사용하면 consumer 앱이 다운되더라도 다른 consumer가 메세지를 대신 관리할 수 있고,    
Pub-Sub를 사용하면 구독자(subscriber)가 다운되더라도 손실된 메세지를 복구하면 토픽에서 사용할 수 있다. 

</br>

### 차이점

핵심 기준은 **모든 consumer들이 모든 메세지를 전부 수신해야 하는가?** 이다.
Pub-Sub구조는 모든 consumer가 모든 메세지를 전부 수신하는 반면 Message Queue의 경우 consumer그룹이 나누어서 큐를 소비한다. 





참고링크:   
https://escapefromcoding.tistory.com/706
