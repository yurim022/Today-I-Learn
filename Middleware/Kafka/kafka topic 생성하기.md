# Kafka Topic 생성하기


먼저 kafka가 다운로드 되어 있다고 가정하자. 

## 1. Zookeeper, Kafka 실행

kafka 파일 경로에 가서 zookeeper를 실행시킨다. 
```
kafka_2.13-3.2.3 % ./bin/zookeeper-server-start.sh ./config/zookeeper.properties
```

그 후에 kafka도 실행시켜준다. 
```
kafka_2.13-3.2.3 % ./bin/kafka-server-start.sh ./config/kafka.properties
```

로그를 통해서도 잘 올라갔는지 확인 할 수 있지만, 네트워크 창을 통해서 서버가 LISTEN 상태인지 확인할 수 있다.
zookeeper는 2181, kafka는 9092를 default 포트로 사용하고 있다. 
```
netstat -anv | grep LISTEN
```
![image](https://user-images.githubusercontent.com/45115557/192152474-6dbf2786-80bc-4613-b9b5-235357fe50f2.png)
