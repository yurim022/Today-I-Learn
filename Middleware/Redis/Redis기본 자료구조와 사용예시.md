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

</br>

# Redis 사용처

* **Cache가 필요한곳**   
   *파레토 법칙마냥 80%의 활동을 20%의 유저가 하기 때문에 20%의 데이터를 캐시하면 서비스 대부분의 데이터를 커버할 수있다. -< DB 접근 줄어듬! 그리고 캐시는 읽기가 유리하다. (쓰기는 어차피 DB 접근..)*
   
* **In-Memory 를 DB로 사용하는 곳**    
   *In-Memory는 접근속도가 빠르므로, 다른 스토리지(SSD,HDD)를 쓰는 것보다 빠르지만 스토리지가 작고 비싸다.ㅠ*

* **Ranking 저장용**

* **Job Queue**
   *redis에 queue처럼 데이터를 넣으면 FIFO 방식으로 데이터를 구독하여 처리한다.*


### 사용 예시

* Web API의 요청을 Key, 응답을 Value로 저장 (Cache)
* 서비스 Access Token 저장
* 요청 수 제한을 위한 Rate Limit
* Ranking 저장을 위한 Ranking 스코어 보드


덧붙이자면, 현재 본인이 개발하고 있는 서비스에서는 Redis를 Jwt 토큰 보관용, 상담 할당 대기 Queue (Job Queue), 세션정보 저장 등 다양하게 활용하고 있다. JWt Access Token의 유저의 권한정보를 redis 에서 관리하고 있다.    
만약 redis 의 해당정보를 시간순으로 정렬하고 싶다거나, 유저id 등의 정보로 정렬하고 싶다면 key에 해당 데이터를 포함하거나, score set 자료구조를 이용할 수 있다. 

</br>


### 대규모 서비스에서 캐시

대규모 서비스에서는 캐시도 1~2대만 사용하는 것이 아니라, 굉장히 많은 수를 사용하게 된다. 대규모로 사용하기 위해 `샤딩` 또는 `Consistent Hashing` 등을 통해 데이터를 저장할 수 있다. 

</br>

참고:   
[대용량 서비스를 위한 아키텍처 with Redis by 강대명](https://fastcampus.app/courses/205143/clips/393035?organizationProductId=15883&hasCodeEditor=false) 강의를 보고 정리한 내용입니다.
