# Spring Batch 도입기2 - chunk적용

Tasklet 으로 spring batch 를 적용했는데, 로컬에서 테스트하던 중 batch를 돌면서 에러가 발생하는 상황을 만났다.    
Fault Tolerance한 batch를 적용하려면 Chunk가 더 유리했다. 
   
</br>

다음 세가지 이유로 chunk를 적용하기로 했다. 

- skipPolicy, exception handling 등이 tasklet에서는 불편 ( exception발생 시 잔여 task도 처리되지 않을 수 있음)
- chunk가 multithread, 동시성 제어 등 제공하는 기능이 많음 (추후 유지보수 용이)
- 서비스 트래픽이 증가하여 대용량 서비스가 되었을때 chunk 유리

Chunk를 적용하기 위해선 ItemReader, ItemProcessor(선택), ItemWriter 가 필요하다.    
이미 제공되는 JdbcBatchItemWriter, JpaCursorItemReader 등 다양한 reader ,writer가 있지만 복잡한 쿼리를 사용하기엔 적합하지 않았다.   
해서 custom 하게 만들기로 했다. 

### CustomItemReader

```java


```
