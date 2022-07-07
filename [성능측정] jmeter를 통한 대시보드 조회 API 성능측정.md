데이터가 너무 방대한 자료에 대한 요청을 했을 시, 응답까지 너무 많은 시간이 걸렸다. 
실시간 데이터에 대해 거의 1년치를 요구했으니,,, 사실상 범위를 줄이거나 롤업을 하는것이 맞지만 속도를 개선할 수 있을까 해서 비동기 방식을 적용해보았다. 

# 결론 요약

## 1. **비동기가 동기보다 빠르나 약 0.6초 로 큰 차이 나지 않음**

### 2. 데이터의 양에 의한 응답속도 차이가 가장 큼. 적절한 granularity 및 filter 를 주는 것이 중요

- **실행시간이 긴 테이블 차트에 장소에 따른 필터 및 granularity  hour → day 로 변경 하였더니 총 응답시간 4.872초 , 필터까지 적용시 총 0.25초로 줄어듬... 요청 granluarity를 작게하여 가져오는 데이터가 너무 많아 지연이 발생했던 것으로 보임**
- **granlarity만 적절하게 줘도 응답시간이 37초 줄어듬**

### 3. Rollup한 데이터의 응답시간이 6초 더 빠르다

- **번외로 airmapRawAndRollup →airmapRollup 데이터로 변경하여 테스트 하였을때 35.881초로 약 6초 감소됨**

1. **장기적으로 성능향상을 위해 드루이드 분산처리 및 쓰레드 관리가 필요할 것으로 보임**

# 성능측정 상세

대시보드 내 차트 총 6개

테스트 환경 : **jmeter**

**<Thread Group>**

```
Number of Threads : 1
Ram-up Period(seconds) : 1
Loop Count : 1
```

### 대시보드 내 차트 Spec

- **2month, granularity hour** 테이블차트 sum_pm_10, sum_pm_25에 대한 min, max, avg 값 (time bucketing, measure bucketing 적용)
    
    41초
    
- **2month, granularity all** spot_dev_nm, dev_dic_cd_nm 에 대한 radar 차트 2개
    
    0.085초 * 2번
    
- **2 month, granularity day** 바차트 spot_dev_nm에 대한 공기질 수치 measure bucketing 0.093초
- **1 day, granularity hour** 라인차트 time bucketing 0.292 초
- **2month granularity day** scatter chart 0.073초

### 특이사항

- 테이블차트에서 현저히 응답속도가 낮음 (**rollup한 데이터와 안한 데이터가 같이 들어가 있는airmapRawAndRollup)**
- 나머지 airmapRollup 에 요청한 데이터는 응답속도 0.1초 안으로 들어옴

## 동기

**평균 응답속도** : **42.338 초**
    
![image](https://user-images.githubusercontent.com/45115557/177742204-74911558-9058-4c69-9945-1fed6763638d.png)

![image](https://user-images.githubusercontent.com/45115557/177742115-cd9f82d7-67c1-4299-9ad6-32222de8ef56.png)


## 비동기

**평균 응답속도** : **41.747 초**

![image](https://user-images.githubusercontent.com/45115557/177742339-017a4d61-cb00-43fd-b225-65517d4a4887.png)
