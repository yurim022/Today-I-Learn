# Spring Batch

채팅상담 백엔드 시스템을 개발하고 있는데, 배치로 상담티켓을 정리해주는 요구사항이 있었다.    
해당기능 을 구현할때 Spring Batch를 구현하는게 어떨까 해서 구글신에게 물어봤다.   



### 장점

- 대용량 데이터 처리에 최적화 되어 고성능
- 로깅, 통계처리, 트랜잭션 관리 등 재사용 가능한 필수 기능 지원
- 자동화
- 예외 사항과 비정상적인 동작에 대한 방어 기능 존재
- 작업 프로세스 구조한 이해하면 비지니스 로직에만 집중 가능
- 스케쥴링 일정에 변경이 생겨도 서비스 재기동 없이 적용 가능

### 사용예시

- 데이터베이스, 파일, 대기열 또는 기타 매체에서 많은 수의 레코드를 읽고 처리한 레코드를 매체(예: 데이터베이스)에 저장하는 경우
- 동시 및 대규모 병렬 처리.
- 단계별, 엔터프라이즈 메시지 중심 처리 서비스
- 종속 단계의 순차 처리
- 일괄 거래
- 예약 및 반복 처리

</br>

결론: redis batch 처리를 함에 있어 무리가 없고, 기능에 비해 (대용량을 다루는 batch job은 아님) 과대하게 구현하는 경향이 있지만, 
향후 적용될 batch job들을 관리하기 편하다는 장점이 있어 적용하기로 결정!! spring batch 적용하기로 땅땅

</br>

#### Batch VS Quartz

둘다 스케쥴링 아닌가? 라고 생각 할 수 있지만 엄밀히 말하자면 다르다. `Quartz는 스케쥴러`의 역할이고 Batch와 같이 대용량 데이터 배치 처리에 대한 기능을 지원하지 않는다.    
Batch 역시 스케쥴 기능을 지원하지 않는다. 그래서 Quartz + Batch 조합으로 많이 사용한다. 



</br>

## Batch 처리방식

</br>

● **Tasklet을 사용한 Task 기반 처리**

- 배치 처리 과정이 비교적 쉬운 경우 쉽게 사용
- 대량 처리를 하는 경우 더 복잡
- 하나의 큰 덩어리를 여러 덩어리로 나누어 처리하기 부적합

● **Chunk를 사용한 chunk(덩어리) 기반 처리**

- ItemReader, ItemProcessor, ItemWriter의 관계 이해 필요
- 대량 처리를 하는 경우 Tasklet 보다 비교적 쉽게 구현
- 예를 들면 10,000개의 데이터 중 1,000개씩 10개의 덩어리로 수행

</br>

## 적용

우선 기본적인 Spring Batch의 구조에 대해 알고 싶다면 [Spring-Batch란-이해하고-사용하기](https://khj93.tistory.com/entry/Spring-Batch%EB%9E%80-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B3%A0-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)
글을 참고하자. Job, Step, Execution 등 구성요소에 대한 설명이 잘 되어있다.    


chunk 방식의 경우 








참고링크:    
[https://prodo-developer.tistory.com/164](https://prodo-developer.tistory.com/164) (Task Chunk 방식 차이)

[https://www.baeldung.com/spring-batch-tasklet-chunk](https://www.baeldung.com/spring-batch-tasklet-chunk) (tasklet 예시)

[https://gimmesome.tistory.com/204](https://gimmesome.tistory.com/204) (schedular)

[https://github.com/redis-developer/spring-batch-redis/tree/master/subprojects/spring-batch-redis/src/main/java/com/redis/spring/batch](https://github.com/redis-developer/spring-batch-redis/tree/master/subprojects/spring-batch-redis/src/main/java/com/redis/spring/batch) (spring batch-redis example)

[https://stackoverflow.com/questions/41927582/accessing-job-parameters-spring-batch](https://stackoverflow.com/questions/41927582/accessing-job-parameters-spring-batch) (job parameter 관련)
