# RabbitMQ VS Kafka

깊게 들어가면 방대하지만 우선 간단하게 정리해보겠다.

![image](https://user-images.githubusercontent.com/45115557/189939000-c005fa55-b888-4e24-8553-0ca70ec6fc69.png)



### RabbitMQ
* AMQP(Advanced Message Queuing Protocol) 사용 cf) AMQP: 메시지 지향, 큐잉, 라우팅(P2P, Publisher-Subscriber), 신뢰성, 보안
* 초당 20+ 메시지를 소비자에게 전달
* 메시지 전달 보장, 시스템 간 메시지 전달
* 보로커, 소비자 중심

### Kafka
* 초당 100k+ 이상의 이벤트 처리
* Pub/Sub, Topic에 메시지 전달
* Ack를 기다리지 않고 전달 가능 (빠름, Ack를 기다리는 설정도 있음)
* 생산자 중심

### 한줄요약

안정성이 높아야 하는 소규모 데이터에서는 RabbitMQ를, 대용량 데이터 처리는 Kafka를.

출처:
https://www.confluent.io/blog/kafka-fastest-messaging-system/
https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C-%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C%EC%84%9C%EB%B9%84%EC%8A%A4
