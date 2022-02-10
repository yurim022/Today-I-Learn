
## COALESCE() 함수

지정한 표현식 중에 NULL 이 아닌 첫번째 값 반환. 

```
select coalesce (VALUE1,0) from SAMPLE
```

이라 할때 VALUE가 100 이라면 100을, VALUE가 null 이라면 0을 반환한다. 
