# Redis 란 

Redis는 크게 `key-value(No-SOL)`로 이루어진 `인메모리 데이터 저장소`이다.    
일반적으로 api의 결과를 키에 맞춰서 저장하거나, 유저의 정보를 저장해 놓는 등 `캐시 용도`로 많이 사용하고 있다.    
(현재 내가 개발중인 서비스에서도 상담session 정보를 저장하기 위한 용도로 사용 중이다.)    
   
클러스터 모드를 제공하여 `고가용성의 아키텍처`를 지원하지만 `메모리 파편화`가 일어날 수 있다는 문제가 있다.    
redis를 db로 사용하는 경우도 있는데, 이 때 백업은 리플리케이션이나 rdb aof 같은 것으로 스냅샷을 남겨서 이용하기도 한다. 


## 자료구조 

* Strings
* List
* Set
* Hash
* Sorted Set  (score를 줌)


# Redis 사용처

* Cache가 필요한곳
* In-Memory 를 DB로 사용하는 곳
* Ranking 저장용
* Job Queue

### 사용 예시

* Web API의 요청을 Key, 응답을 Value로 저장 (Cache)
* 





참고:
[대용량 서비스를 위한 아키텍처 with Redis by 강대명](https://fastcampus.app/courses/205143/clips/393035?organizationProductId=15883&hasCodeEditor=false)
