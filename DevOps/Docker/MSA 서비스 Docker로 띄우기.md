# MSA Docker로 띄우기

위 MSA 아키텍쳐는 [e-commerce](https://github.com/yurim022/e-commerce.git) 의 모듈들을 업로드 하는 과정이다. 

</br>

## 네트워크 생성

Docker에서 container간 network를 통해 통신할 수 있다. 
먼저 docker에서 기본으로 제공되는 network에는 Bridge, Host, None 이 있다. 

* **Bridge network** : 호스트와 별도의 가상의 네트워크를 만들고 컨테이너를 배치해서 사용하는 방식
* **Host network** : 호스트의 네트워크 환경을 게스트 네트워크에서 그대로 사용. 포트 포워딩 없이 내부 어플리케이션 사용
* **Non network** : 네트워크를 사용하지 않음. lo네트워크만 사용, 외부와 단절

![image](https://user-images.githubusercontent.com/45115557/202164803-d3dce4d6-5f5b-4349-8d54-cbd5a76647d8.png)

</br>

### Docker network driver bridge

Docker를 설치하게 되면 자동으로 Host machine의 Network interface에 Docker0 이라는 Virtual interface가 생성된다.    
Docker0은 host machine의 네트워크에 생성이되며 Container가 생성될때 사용자가 특정 network driver를 설정한 것이 아니라면 default로 Docker0이라는 Network interface에 연결이 된다. 
각 Container의 veth 인터페이스와 바인딩되며 Host Os의 eth0 인터페이스와 이어주는 역할을 한다. Docker0은 일반적인 인터페이스가 아니고 virtual ethernet bridge이다. 

<img width="431" alt="image" src="https://user-images.githubusercontent.com/45115557/202166381-57e7a278-9e8e-4d1e-b306-24b9d3764c83.png">

Docker0의 Gateway는 자동으로 172.17.0.1로 설정되며 16 bit netmask(255.255.0.0)로 설정된다. 이 ip는 DHCP를 통해 할당받는 것이 아니고 docker 내부 로직에 의해 자동 할당 되는 것이다. 

</br>

### 사용자 정의 네트워크 생성

```
docker network create --gateway 172.18.0.1 --subnet 172.18.0.0/16 ecommerce-network
```

gateway와 subnet을 지정하지 않고 만들수도 있지만, 이때 ip를 지정해서 컨테이너를 띄우고 싶은 경우 오류가 날 수 있다. 

</br>

### 네트워크 정보 확인

```
docker inspect ecommerce-network
```

inspect 명령어를 통해 네트워커의 gateway,subnet, 연결된 container 등의 정보를 확인할 수 있다. 

![스크린샷 2022-11-16 오후 11 46 00](https://user-images.githubusercontent.com/45115557/202211957-306a3fc2-7f76-4488-aa8d-fc0ed068f3df.png)


일반적인 컨테이너는 하나의 guest os라고 생각하면 된다. 각각의 guest os마다 고유의 ip가 할당이 되게 되는데,    
**같은 network에 포함된 컨테이너는 ip address 외에도 container id, container 이름을 통해서 통신할 수 있다.**    
ip address는 변경될 수 있기 때문에 컨테이너 이름을 통해 네트워크 해주어야 한다. 

</br>

## 컨테이너 기동

</br>

### RabbitMQ
rabbitmq 를 기동해준다. 

```
docker run -d --name rabbitmq --network ecommerce-network \
-p 15672:15672 -p 5672:5672 -p 15671:15671 -p 5671:5671 -p 4369:4369 \
-e RABBITMQ_DEFAULT_USER=guest -e RABBITMQ_DEFAULT_PASS=guest rabbitmq:management
```
-p 옵션으로 각각 호스트에서 접속할 포트 매핑도 해주고, -e로 환경변수 값도 세팅해준다. 

</br>

### Config-Service

서비스를 컨테이너화 하기 위해 DockerFile을 만들어준다. 

```
FROM openjdk:17-ea-11-jdk-slim
VOLUME /tmp
COPY apiEncryptionKey.jks apiEncryptionKey.jks
COPY target/config-service-1.0.jar ConfigServer.jar
ENTRYPOINT ["java","-jar","ConfigServer.jar"]
```
   
만들어준 도커파일을 실행시키는데 내 도커허브 아이디는 yurimming 이니까 앞에 yurimming을 붙여줬다. 저장하고자 하는 repositroy에 맞게 붙여주면 된다.

```
docker build -t yurimming/config-service:1.0 .
```
   
   
그다음 생성된 이미지를 실행시켜주는데, 여기서 application.yml 파일의 설정정보를 덮어쓸 수 있다. rabbitmq.host 값이 기존에 127.0.0.1이 되어있었는데 docker에서 다른 ip를 가지므로 같은 네트워크 안에서 이름으로 접근하도록 `-e "spring.rabbitmq.host=rabbitmq"`를 해준다. 


```
docker run -d -p 8888:8888 --network ecommerce-network -e "spring.rabbitmq.host=rabbitmq" -e "spring.profiles.active=default" --name config-service yurimming/config-se
rvice:1.0
```
</br>

### Discovery-Service

도커파일 빌드하는 부분은 위에서 했으니 생각하고, 이번에는 config-service 정보를 가져오기 위해 docker run 할때 `-e "spring.cloud.config.uri=http://config-service:8888"` 로 설정값을 오버라이드 해서 사용해 주었다.

```
docker run -d -p 8761:8761 \
--network ecommerce-network \
-e "spring.cloud.config.uri=http://config-service:8888" \
--name discovery-service yurimming/discovery-service:1.0
```

커스텀하게 생성한 도커이미지를 허브나 기타 리포지토리에 올리고 싶다면 해당 리포지토리에 login 한 후 `docker push [이미지]` 를 해주면 된다. 

```
docker push yurimming/discovery-service:1.0
```

</br>

### Apigateway-service

Dockerfile생성 및 빌드 과정을 거친후 마찬가지로 run 해준다. 

```
docker run -d -p 8000:8000 --network ecommerce-network \
> -e "spring.cloud.config.uri=http://config-service:8888" \
> -e "spring.rabbitmq.host=rabbitmq" \
> -e "eureka.client.serviceUrl.defaultZone=http://discovery-service:8761/eureka/" \
> --name apigateway-service yurimming/apigateway-service:1.0
```

</br>

### Order-service

```
docker run -d --network ecommerce-network \
--name order-service \
-e "spring.cloud.config.uri=http://config-service:8888" \
-e "spring.rabbitmq.host=rabbitmq" \
-e "spring.zipkin.base-url=http://zipkin:9411" \
-e "spring.datasource.url=jdbc:mariadb://mariadb:3306/mydb" \
-e "eureka.client.serviceUrl.defaultZone=http://discovery-service:8761/eureka/" \
-e "logging.file=/api-logs/orders-ws.log" \
yurimming/order-service:1.0
```
user-service와 catalog-service도 설정값들을 오버라이드 해야 되는 것들을 파악해서 올려준다. 

</br>

### Mariadb

다음과 같이 도커파일을 만들어준다. 기존에 로컬에서 사용했던 db 내용을 사용하기 위해 `cp -R /usr/local/var/mysql .` 로 `mysql_data`디렉토리(다른 디렉토리이름도 상관없다)에 mysql 폴더를 옮겨준다. 

그리고 Dockerfile을 다음과 같이 생성한다. 

```
FROM mariadb
ENV MYSQL_ROOT_PASSWORD test1357
ENV MYSQL_DATABASE mydb
COPY ./mysql_data/mysql /var/lib/mysql   //COPY [Host경로] [컨테이너 내부경로]
EXPOSE 3306
ENTRYPOINT ["mysqld","--user=root"]
```

그리고 빌드한 뒤 run 해준다.

```
docker run -d -p 3306:3306 --name mariadb yurimming/my_mariadb:1.0
```
다른 서비스에서 db에 접근하기 위해 접근 권한을 준다. 여기에선 모든 데이터베이스에 root의 접속권한을 주도록 한다.  
```
docker exec -it [mariadb container id] mysql -u root -p

>grant all privileges on *.* to 'root'@'%' identified by 'test1357'; //grant all privileges on [datbase] to [접속권한] identified by [password];
>flush privileges;
```

해당 권한설정을 안해주면 아래와 같은 에러가 나면서 order-service가 죽게된다. 

![image](https://user-images.githubusercontent.com/45115557/206972145-7a0820c3-b723-4c45-a1b3-3030a69b46a9.png)


아, 네트워크 추가하는걸 까먹었다. 그렇다면 `connect` 명령어로 이미 생성된 컨테이너에 네트워크를 연결시켜주면 된다. 

```
docker network connect ecommerce-network mariadb
```

### Kafka 

kafka는 zookeeper에 의존성이 있으므로 docker-compose로 배포해보자. 
여기에서 
```Dockerfile
    networks:
      my-network:
        ipv4_address: 172.18.0.100
```
와 같이 어떤 포트로 매핑할건지 명시되어 있는데, 이는 기존 코드에 bootstrapserver가 localhost로 되어있기 때문에 다른 컨테이너에서 접속이 안될 수 있어 ecommerce-network의 대역폭 안에 있는 값으로 매핑해주었다. 현재는 single cluster 버전으로 배포하였으며 원래 kafka는 3개 이상 띄우는 것을 권장한다. 


```
version: '2'

networks:
  my-network: 
    name: ecommerce-network


services:
  zookeeper:
    image: wurstmeister/zookeeper
    ports:
      - "2181:2181"
    networks:
      my-network:
        ipv4_address: 172.18.0.100

  kafka:
    # build: .
    image: wurstmeister/kafka
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_HOST_NAME: 172.18.0.101
      KAFKA_CREATE_TOPICS: "test:1:1"
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    depends_on:
      - zookeeper
    networks:
      my-network:
        ipv4_address: 172.18.0.101
```
docker-compose 파일을 작성하여 준 뒤에 아래 명령어로 실행시켜준다. 

```
docker-compose -f docker-compose-single-broker.yml up -d
```

</br>

### zipkin

```
docker run -d -p 9411:9411 \
--network ecommerce-network \
--name zipkin openzipkin/zipkin
```

</br>

### premetheus

먼저 프로메테우스의 경우, prometheus.yml의 설정정보로 어느 job에 접근할지 정의해주고 있다. 
호스트 파일의 prometheus.yml 파일과 매핑시켜주고, 해당 파일의 값들은 컨테이너이름으로 접근하도록 설정정보를 추가해준다. 

**prometheus.yml**
```
    static_configs:
      - targets: ["prometheus:9090"]



  - job_name: 'user-service'
    scrape_interval: 15s
    metrics_path: '/user-service/actuator/prometheus'
    static_configs:
    - targets: ['apigateway-service:8000']


  - job_name: 'apigateway-service'
    scrape_interval: 15s
    metrics_path: '/actuator/prometheus'
    static_configs:
    - targets: ['apigateway-service:8000']


  - job_name: 'order-service'
    scrape_interval: 15s
    metrics_path: '/order-service/actuator/prometheus'
    static_configs:
    - targets: ['apigateway-service:8000']

```

docker run

```
docker run -d -p 9090:9090 --network ecommerce-network --name prometheus \
-v /Users/lena/work/prometheus.yml:/etc/prometheus/prometheus.yml \
prom/prometheus
```

</br>

### grafana

```
docker run -d -p 3000:3000 --network ecommerce-network --name grafana grafana/grafana
```





</br>
 
참고링크:   
https://jangseongwoo.github.io/docker/docker_container_network/   
