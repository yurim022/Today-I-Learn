# CQRS 패턴이란

CQRS : Command and Query Responsibility Segregation 의 약자로 **명령과 조회의 책임을 분리** 하는 것이다. 


## 기존 아키텍처 데이터 접근 방식 

![image](https://user-images.githubusercontent.com/45115557/204089552-d27e7a40-b9b2-4b33-845a-d5acaf8cec55.png)

일반적인 아키텍처에서는 비지니스 로직이 데이터액세스 클래스를 호출하여 DB에 접근한다.    
이 같은 아키텍처에서 비지니스 로직에 따라 데이터에 접근할 경우, DB입장에서는 하나의 트랜잭션 형태로 수행될 것이고 
해당 트랜젝션의 쿼리들이 여러개일 경우 (select 100개, update 1개) 트랜잭션 격리로 인해 데이터 경합이 발생 할 수 있다. 







참고링크:    
https://velog.io/@zihs0822/CQRS-%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80   
