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

</br>

### CustomItemReader

```java

@Component
@RequiredArgsConstructor
public class CustomItemReader implements ItemStreamReader<CsHistoryBas> {

    @Value("${batch.expire-day}")
    private EXPIRE_DAY;

    @Value("${batch.period-day}")
    private long PERIOD_DAY;

    private final CustomRepository customRepository;

    private List<CsHistoryBas> items;
    private AtomicInteger index = new AtomicInteger();


    @Override
    public CsHistoryBas read() throws Exception, UnexpectedInputException, ParseException, NonTransientResourceException {

        CsHistoryBas csHistoryBas = null;
        int currentIndex = index.getAndIncrement();
        if ( currentIndex < items.size() ) {
            csHistoryBas = items.get(currentIndex);
        }

        return csHistoryBas;
    }

    @Override
    public void open(ExecutionContext executionContext) throws ItemStreamException {
        items = getHistories();
        index.set(0);
    }

    @Override
    public void update(ExecutionContext executionContext) throws ItemStreamException {
    }

    @Override
    public void close() throws ItemStreamException {
        items.clear();
        index.set(0);
    }

    private List<CsHistoryBas> getHistories(){
        LocalDateTime now = LocalDateTime.now();
        // 180일 전, 1일씩 데이터 삭제
        LocalDateTime searchBefore = now.minus(REMOVE_ES_MESSAGE_EXPIRE_DAY, ChronoUnit.DAYS);
        LocalDateTime searchAfter = searchBefore.minus(REMOVE_ES_MESSAGE_PERIOD_DAY, ChronoUnit.DAYS);
        List<CsHistoryBas> csHistories = customRepository.getCsHistoriesByDateTime(searchAfter, searchBefore);
        return csHistories;
    }

}
```

ItemReader가 매번 생성되는 것이 아니고, Bean으로 등록하여 작동할때마다 데이터 값이 갱신되어야 했기 때문에 open, update, close를 사용할 수 있는 ItemStreamReader를 사용하였다. 

</br>

### CustomItemWriter

```java


@Component
@RequiredArgsConstructor
public class CustomItemWriter implements ItemStreamWriter<CsHistoryBas> {

    private final LogSvcES logSvcES;

    @Override
    public void open(ExecutionContext executionContext) throws ItemStreamException {
    }

    @Override
    public void update(ExecutionContext executionContext) throws ItemStreamException {
    }

    @Override
    public void close() throws ItemStreamException {
    }

    @Override
    public void write(List<? extends CsHistoryBas> csHistories) throws Exception {
        for (CsHistoryBas csHistoryBas : csHistories) {
            logSvcES.deleteMessage(csHistoryBas.getCompId(), csHistoryBas.getTicktId());
        }
    }
}


```

마찬가지로 ItemStreamWriter 를 구현해준다. 


</br>

### BatchConfig

```java
 @Bean
    public Step removeEsMessageStep() {

        return stepBuilderFactory.get(REMOVE_ES_MESSAGE_STEP)
                .<CsHistoryBas,CsHistoryBas>chunk(REMOVE_ES_MESSAGE_CHUNK_SIZE != null ? REMOVE_ES_MESSAGE_CHUNK_SIZE : DEFAULT_CHUNK_SIZE)
                .reader(customItemReader)
                .writer(customItemReaderItemWriter)
                .faultTolerant()
//                .skip(NoSuchElement.class)
//                .skipLimit(REMOVE_ES_MESSAGE_SKIP_LIMIT != null ? REMOVE_ES_MESSAGE_SKIP_LIMIT : DEFAULT_SKIP_LIMIT)
                .noRetry(NullPointerException.class)
                .build();
    }

```

다음과 같이 적용해준다!
