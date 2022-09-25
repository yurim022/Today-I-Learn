# Kafka Topic 생성하기


먼저 kafka가 다운로드 되어 있다고 가정하자. [kafka 다운로드](https://kafka.apache.org/)

## 1. Zookeeper, Kafka 실행

kafka 파일 경로에 가서 zookeeper를 실행시킨다. 
```
./bin/zookeeper-server-start.sh ./config/zookeeper.properties
```

그 후에 kafka도 실행시켜준다. 
```
./bin/kafka-server-start.sh ./config/kafka.properties
```

로그를 통해서도 잘 올라갔는지 확인 할 수 있지만, 네트워크 창을 통해서 서버가 LISTEN 상태인지 확인할 수 있다.
zookeeper는 2181, kafka는 9092를 default 포트로 사용하고 있다. 
```
netstat -anv | grep LISTEN
```
![image](https://user-images.githubusercontent.com/45115557/192152474-6dbf2786-80bc-4613-b9b5-235357fe50f2.png)
<br/><br/><br/><br/>

## 2. topic 생성

```
./bin/kafka-topics.sh --create --topic quickstart-events --bootstrap-server localhost:9092 --partitions 1
```
다음 명령어로 topic을 생성할 수 있다. 이때, `kafka-could-not-be-established-Broker-may-not-be-available-orgapachekafkaclientsNetworkClient` 와 같은 에러가 났다. 
<br/><br/><br/>
## 3. 에러 해결

```
$ vi config/server.properties
```

config 파일로 이동해서 `#listeners = PLAINTEXT://your.host.name:9092` 주석처리된 부분을 해지하고 IP를 넣어준다. `advertised.listeners` 도 주석해제 해준다. 
`advertised.listeners`는 카프카 클라이언트나 커맨드 라인 툴을 브로커와 연결할 때 쓰인다. 
```
############################# Socket Server Settings #############################

# The address the socket server listens on. It will get the value returned from
# java.net.InetAddress.getCanonicalHostName() if not configured.
#   FORMAT:
#     listeners = listener_name://host_name:port
#   EXAMPLE:
#     listeners = PLAINTEXT://your.host.name:9092
listeners=PLAINTEXT://localhost:9092

```
<br/><br/><br/>
## 4. Topic 정보 확인

### topic 목록 확인
```
./bin/kafka-topics.sh --bootstrap-server localhost:9092 --list
```

![image](https://user-images.githubusercontent.com/45115557/192152953-05396b2c-cd20-4d63-a66e-e0c5edd832f6.png)


### topic 정보 확인
```
./bin/kafka-topics.sh --describe -topic quickstart-events --bootstrap-server local
host:9092
```

![image](https://user-images.githubusercontent.com/45115557/192153044-18f72205-359d-46e8-9033-f12f32935ee3.png)

잘 생성된걸 확인할 수 있다! 휴!



<br/><br/><br/>

참고링크: 
https://somjang.tistory.com/entry/Kafka-could-not-be-established-Broker-may-not-be-available-orgapachekafkaclientsNetworkClient-해결-방법 .  
https://woonizzooni.tistory.com/entry/Mac-listen-%ED%8F%AC%ED%8A%B8-pid-%ED%99%95%EC%9D%B8-%EB%B0%A9%EB%B2%95-TCPUDP-%EC%84%B8%EC%85%98-%ED%99%95%EC%9D%B8-%EB%B0%A9%EB%B2%95   

https://velog.io/@jmjmjames/Kafka-could-not-be-established.-Broker-may-not-be-available.-org.apache.kafka.clients.NetworkCilent-%ED%95%B4%EA%B2%B0%EB%B0%A9%EB%B2%95
