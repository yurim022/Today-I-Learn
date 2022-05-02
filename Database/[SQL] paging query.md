### paging query

데이터가 너무 많을 경우, 불러오는데 너무 많은 시간이 걸려 대안으로 paging 처리를 하게 되었다.
쿼리는 생각보다 간단하다.

```

SELECT * FROM history_table
WHERE id = 1
OFFSET 0 ROWS FETCH NEXT 10 ROWS ONLY

```

다음과 같이 OFFSET {page}로 paging 시작지점,
몇개의 데이터를 가져올 것인지 ROWS FETCH NEXT {size} ROWS ONLY
정의해주면 된다. LIMIT을 써도 된다!
