# MSA Docker로 띄우기2

앞서 작성한 글대로 컨테이너 환경을 구성한 뒤 docker-compose 파일로 서비스를 실행하였다. 

```
version: '2'
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

  rabbitmq:
    image: rabbitmq:management
    ports:
      - "15671:15671"
      - "15672:15672"
      - "5671:5671"
      - "5672:5672"
      - "4369:4369"
    environment:
      RABBITMQ_DEFAULT_USER: guest
      RABBITMQ_DEFAULT_PASS: guest
    networks:
      my-network:

  config-service:
    image: yurimming/config-service:1.0
    ports:
      - "8888:8888"
    environment:
      spring.rabbitmq.host: rabbitmq
      spring.profiles.active: default
    depends_on:
      - rabbitmq
    networks:
      my-network:

  discovery-service:
    image: yurimming/discovery-service:1.0
    ports:
      - "8761:8761"
    environment:
      spring.cloud.config.uri: http://config-service:8888
    depends_on:
      - config-service
    networks:
      my-network:

  apigateway-service:
    image: yurimming/apigateway-service:1.0
    ports:
      - "8000:8000"
    environment:
      spring.cloud.config.uri: http://config-service:8888
      spring.rabbitmq.host: rabbitmq
      eureka.client.serviceUrl.defaultZone: http://discovery-service:8761/eureka/
    depends_on:
      - discovery-service
    networks:
      my-network:

  mariadb:
    image: yurimming/my-mariadb:1.0
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: test1357
      MYSQL_DATABASE: mydb
    networks:
      my-network:

  zipkin:
    image: openzipkin/zipkin
    ports:
      - "9411:9411"
    networks:
      my-network:

  prometheus:
    image: prom/prometheus
    ports:
      - "9090:9090"
    volumes:
      - /Users/lena/work/prometheus.yml:/etc/prometheus/prometheus.yml
    networks:
      my-network:

  grafana:
    image: grafana/grafana
    ports:
      - "3000:3000"
    networks:
      my-network:

  user-service:
    image: yurimming/user-service:1.0
    environment:
      spring.cloud.config.uri: http://config-service:8888
      spring.rabbitmq.host: rabbitmq
      spring.zipkin.base-url: http://zipkin:9411
      eureka.client.serviceUrl.defaultZone: http://discovery-service:8761/eureka/
      logging.file: /api-logs/users-ws.log
    depends_on:
      - apigateway-service
    networks:
      my-network:
        ipv4_address: 172.18.0.12

  order-service:
    image: yurimming/order-service:1.0
    environment:
      spring.cloud.config.uri: http://config-service:8888
      spring.rabbitmq.host: rabbitmq
      spring.zipkin.base-url: http://zipkin:9411
      eureka.client.serviceUrl.defaultZone: http://discovery-service:8761/eureka/
      spring.datasource.url: jdbc:mariadb://mariadb:3306/mydb
      logging.file: /api-logs/orders-ws.log
    depends_on:
      - apigateway-service
    networks:
      my-network:
        ipv4_address: 172.18.0.13

  catalog-service:
    image: yurimming/catalog-service:1.0
    environment:
      spring.cloud.config.uri: http://config-service:8888
      spring.rabbitmq.host: rabbitmq
      spring.zipkin.base-url: http://zipkin:9411
      eureka.client.serviceUrl.defaultZone: http://discovery-service:8761/eureka/
      logging.file: /api-logs/catalogs-ws.log
    depends_on:
      - apigateway-service
    networks:
      my-network:
        ipv4_address: 172.18.0.14

networks:
  my-network:
    external: true
    name: ecommerce-network
```

내 도커허브 레포지토리의 이미지를 사용하여 작성한 다음 docker-compose 파일을 실행시켜주었다.  

```
docker-compose up -d
```

