[스프링-클라우드-마이크로서비스](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C-%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C%EC%84%9C%EB%B9%84%EC%8A%A4) 강의를 기반으로 공부한 내용입니다. 

   
# CQRS 패턴이란

CQRS : Command and Query Responsibility Segregation 의 약자로 **명령과 조회의 책임을 분리** 하는 것이다.    
즉 쉽게 말해서 상태를 변경할 수 있는 command와 (Create, Insert, Update, Delete) 조회를 담당하는 call (Select, Read) 이라는 파트 2개를 분리해서 각각의 작업을 처리해주는 것이다. 

![image](https://user-images.githubusercontent.com/45115557/204132735-c9c9100a-a8f0-4fc6-b3cb-92a8151863fe.png)

전통적인 CRUD 아키텍처 기반에서 Application을 개발 및 운영하다가 보면 자연스럽게 Domain Model의 복잡도가 증가하고 그에 따라 유지보수의 비용이 증가하고 Domain model은 설계의 방향과 다르게 변질된다. 두 책임을 분산시킴으로써 Read 성능을 향상시키고 도메인 복잡성을 낮추기 위해 CQRS 패턴을 사용할 수 있다. 

</br>

## CQRS 종류

### 전통적인 CRUD 시스템

![전통적인](https://user-images.githubusercontent.com/45115557/204135397-6aaa84e2-2ef6-4f89-b952-896619d5c404.png)

전통적인 시스템은 다음과 같은 계층 구조를 가진다. 이에 CQRS를 적용하기 위한 방법은 최소 3가지 방법이 있다. 

</br>

### 1. Simple CQRS Architecture

![1](https://user-images.githubusercontent.com/45115557/204135862-a91caac8-2593-4e37-91e9-1332234a326e.png)

단일 data store에 Command Query Model을 분리된 계층으로 나누는 방식이다. Database(RDBMS)는 분리하지 않고 기존 구조를 유지하고 Command 와 Query Model로 분리하는 수준으로 간단하게 적용할 수 있다. 

#### 장점 

훨씬 단순하게 구현 및 적용할 수 있다. 

#### 단점

동일한 Database 사용에 따른 성능상 문제점은 개선하지 못한다. 

</br>

### 2. CQRS with separated persistence mechanisms

![2](https://user-images.githubusercontent.com/45115557/204136012-0ead0e26-5545-4077-9734-f1bab81c0787.png)

해당 방식은 Command 용 DB와 Query용 DB를 분리하고 별도의 Broker를 통해 둘간의 Data를 동기화 처리하는 방식이다. 
이 경우 데이터를 조회하려는 서비스들은 서비스에 맞는 저장소를 선택할 수 있기 때문에 polyglot 구조로도 구성할 수 있다.    
각가의 Model에 맞게 저장소 (RDBMS, NoSQL, Cache)를 튜닝해서 사용할 수 있다. 

* *polyglot : 다수의 Database를 혼용해서 사용하는 것*

#### 장점

simple CQRS에서 거론도의 DB사용에 발생하는 성능 문제를 해결할 수 있다. 

#### 단점

동기화 처리를 위한 Broker의 가용성과 신뢰도가 보장되어야 한다. 

</br>

### 3. EventSourcing Model

![3](https://user-images.githubusercontent.com/45115557/204136154-c28ad11e-a369-4f16-8cbd-a49f256333b8.png)

해당 방식은 이벤트 소싱(Event Sourcing)을 적용한 구조이다.    

* *이벤트 소싱 : Application 내의 모든 활동을 이벤트로 전환해서 이벤트 스트림(Event Stream)을 별도의 DB에 저장하는 방식*

Event Sourcing Model이란 이벤트스트림을 저장하는 데이터베이스에는 오직 데이터 추가만 가능하고, 계속적으로 쌓이는 데이터를 구체화시키는 시점에서 그때까지 
구축된 데이터를 바탕으로 조회 대상 데이터를 작성하는 방법을 의미한다. 즉, Application 내 상태변경을 이력으로 관리하는 패턴의 발전된 형태이다. 
이벤트 소싱의 이벤트 스트림은 오직 추가만 가능하고, 필요로 하는 시점에 구체화 단계를 가지는 처리과정이 CQRS의 모델 분리 관점에서 잘 맞기 때문에 주로 선택한다.    

*cf) CQRS 패턴에 이벤트 소싱은 필수가 아니지만 이벤트 소싱에는 CQRS가 필요하다.*

</br>

### 서비스 적용

![image](https://user-images.githubusercontent.com/45115557/204136470-18917551-47b9-4287-a6e2-d290cf16b69c.png)

3번의 방식으로 만들었던 e-commerce 어플리케이션에 적용을 해보자. 
사용자가 주문을 POST /orders 로 요청을 했을 때 주문을 업데이트 하거나 생성, 삭제 할 수 있고 GET /orders로 주문 자체를 조회할 수 있다.    
요청이 들어오면 아래 시퀀스로 실행이 될 수 있다.    

1) command 모델로 주문 update 요청
2) kafka topic에 데이터를 저장
3) topic에 저장된 데이터 값은 연결되어 있는 micro service, email 시스템으로 전달
4) event handler에서 topic에 전달된 값을 catch
5) 실제 값을 database에 update

이 방식으로 상태값을 전부 topic 에 기록할 수 있고, topic에 기록된 실제 데이터의 마지막 상태값을 데이터베이스에 반영할 수 있다.     
query 모델에서 최종 상태값만 가지고 와서 해당하는 값을 클라이언트에 전달시켜줌으로써 클라이언트는 마지막에 주문된 내역의 상태값을 확인할 수 있다. 

</br>

## CQRS 장점

* **Independent Scaling** : 읽기 모델과 쓰기 모델을 필요에 따라 독립적으로 확장 가능
* **Optimized data schemas** : 읽기 모델은 쿼리에 최적화된 스키마 사용 가능
* **Security** : 올바른 도메인 엔티티만 데이터 쓰기를 수행할 수 있는지 쉽게 확인 가능
* **Seperation of concerns** : 복잡한 비지니스 로직 구현은 대부분 쓰기 모델에 속하며, 읽기 모델은 간단하게 구현된다. 그에 따라 읽기와 쓰기를 분리하면 유지관리가 더 쉽고 유연한 모델이 구현될 수 있다. 
* **Simpler queries** : DB에 논리적인 View가 아닌, Materialized View(물리적인 View)를 저장함으로써 애플리케이션에서 복잡한 조인이 사용된 쿼리문을 피할 수 있다. 

</br>

## CQRS 단점

* **Complexity** : 아이디어는 간단하나, 해당 아이디어가 이벤트 소싱 패턴까지 이어질 경우 구현이 어렵다. 
* **Messaging** : CQRS 패턴에서 메시징이 필요하진 않지만 메시징을 사용해서 명령을 처리하고 이벤트를 게시하는 것이 일반적이다. (Kafka) 
메시징을 사용하는 경우, Application은 메시지 실패 또는 중복 메시지 처리에 대한 로직을 상세하게 구현해야 한다. 
* **Eventual consistency** : 읽기 모델과 쓰기 모델이 서로 다른 DB로 분리되었을 경우, 읽기 모델을 쓰기 모델의 변경사항이 실시간으로 반영되도록 구현해야 한다.
만약 유저에게서 과거의 데이터를 기준으로 요청이 발생했을 경우 처리하는데 어려움이 발생한다. 

</br>







참고링크:    
https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C-%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C%EC%84%9C%EB%B9%84%EC%8A%A4   
https://velog.io/@zihs0822/CQRS-%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80   
https://azderica.github.io/02-architecture-msa/.  

