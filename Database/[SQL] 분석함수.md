## 분석함수

프로젝트에서 특정값이 max인 row들을 추출해야 했는데, group by 를 쓰면 나머지 row 들을 추출할 수도 없고 subquery를 쓰기엔 쿼리가 너무 복잡했다. 
여기에서 group by 와 비슷하면서 다른 column 값들도 보존할 수 있는 partition by를 찾게 되었다. 그 전에 더 큰 개념인 분석함수를 알아보자. 


```
SELECT
    분석함수 OVER([PARTITION BY 칼럼] [ORDER BY 칼럼] [WINDOWING 절])
FROM 테이블;
```
분석함수는 OVER 절을 통해 집계하는 것인데, select 문에 있기 때문에 where, group by, having, select 후에 실행된다. 간단한 요구사항이라면 group by가 나을 수 있다. 

### 분석할 수 있는 것
+ 순위함수 : RANK(), ROW_NUMBER(), DENSE_RANK() 
+ 집계함수 : COUNT(), AVG(), SUM()
+ 행순서 관련 함수 : LEAD(), LAG(), FIRST_VALUE(), LAST_VALUE
+ 분위 함수 : NTITLE() --> NTITLE(4) 면 사분위값으로 영역 나눌 수 있음



## WINDOWING 절 구조
![image](https://user-images.githubusercontent.com/45115557/163857399-fc5c3461-b5fd-45ee-8bbc-d9338e9788c6.png)

범위를 지정할 구문으로 ROWS/RANGE가 사용 가능한데, 
ROWS는 물리적인 결과행을 기준으로, RANGE는 논리적인 범위를 지정한다.(partition by 한 범위 기준) 


## PARTITION BY vs GROUP BY

직군별 연봉테이블 정보를 조회한다 가정하자.

*GROUP BY*
```
select job, max(salary) from salary_table group by salary
```

다음 쿼리 실행시 각 group by 에 해당하는 column만 집계 가능하고, 각 group 당 집계 값도 1가지만 추출된다. 

*PARTITION BY*
```
select job, name, age, max(salary) over (partition by job) from salary_table
```
다음 쿼리 실행 시 group by가 아닌 column 값도 조회가 가능하며, max값 필드가 각 row들 옆에 추가되어 집계되기 전에 column 값들도 볼 수 있다.

응용해서 분야별 max값 1개만 추출하고, 여러개의 column도 추출하고 싶다면 아래와 같이 할 수 있다. 
```
select job, name, age, max(salary) over (partition by job order by salary desc rows current row) from salary_table
```




참고링크:
https://myjamong.tistory.com/188
https://lee1535.tistory.com/19
https://project-notwork.tistory.com/53
