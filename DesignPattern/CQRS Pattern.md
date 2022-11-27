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

