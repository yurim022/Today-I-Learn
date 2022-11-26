[스프링-클라우드-마이크로서비스](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C-%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C%EC%84%9C%EB%B9%84%EC%8A%A4) 강의를 기반으로 공부한 내용입니다. 

   
# CQRS 패턴이란

CQRS : Command and Query Responsibility Segregation 의 약자로 **명령과 조회의 책임을 분리** 하는 것이다.    
MSA 아키텍처에서 기존 monoithic 형태에서 ACID 가 보장되었다고 한다면, 여러가지 서비스가 연동되어 있는 MSA에서는 여러서비스의 각기다른 DB 가 하나의 요청에 수행되어야 할 경우 ACID를 보장하기 어렵다. 

![image](https://user-images.githubusercontent.com/45115557/204090080-a20200ff-d034-4a15-820b-703336a97efa.png)

위와 같이 order service 에서 주문을 하면 catalog service의 db도 업데이트 되어야 한다고 했을때, order service에서 catalog service의 DB에 직접적으로 접근할 수 없고, 
API 요청이나 다른 방법을 통해 통해 접근해야 한다. 










참고링크:    
https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C-%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C%EC%84%9C%EB%B9%84%EC%8A%A4   
https://velog.io/@zihs0822/CQRS-%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80   
