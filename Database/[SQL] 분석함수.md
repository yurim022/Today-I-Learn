## 분석함수

프로젝트에서 특정값이 max인 row들을 추출해야 했는데, group by 를 쓰면 나머지 row 들을 추출할 수도 없고 subquery를 쓰기엔 쿼리가 너무 복잡했다. 
여기에서 group by 와 비슷하면서 다른 column 값들도 보존할 수 있는 partition by를 찾게 되었다. 


```
SELECT
    분석함수 OVER([PARTITION BY 칼럼] [ORDER BY 칼럼] [WINDOWING 절])
FROM 테이블;
```
분석함수는 OVER 절을 통해 집계하는 것인데, select 문에 있기 때문에 where, group by, having, select 후에 실행된다. 간단한 요구사항이라면 group by가 나을 수 있다. 

### 집계할 수 있는 것
+ 순위함수 : RANK(), ROW_NUMBER(), DENSE_RANK() 
+ 집계함수 : COUNT(), AVG(), SUM()
+ 행순서 관련 함수 : LEAD(), LAG(), FIRST_VALUE(), LAST_VALUE
+ 분위 함수 : NTITLE() --> NTITLE(4) 면 사분위값으로 영역 나눌 수 있음





참고링크:
https://myjamong.tistory.com/188
https://lee1535.tistory.com/19
