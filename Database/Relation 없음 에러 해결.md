# relation does not exist 에러해결하기

분명 tb에 테이블을 만들었는데 자꾸만 없다는 에러가 떴다.   
확인해보면 분명이 테이블이 있는데,,,   

결론적으로, 스키마가 다른 곳에 테이블을 만들었다...ㅋㅋ
그럼 문제를 해결하기 위한 명령어들을 보자.   

#### default schema 확인

```
show search_path;
```
여기서 다른 스키마에 접속해 있었다. (광광...내 삽질 시간)

</br>

#### target schema 변경

```
set search_path = new_schema
```

</br>

여기서 끝이 아니다. 권한도 부여해줘야 된다!!!!

</br>

#### 테이블 권한 조회

```
select * from information_schema.role_table_grants where grantee = '계정명'
```

</br>

#### 권한 추가

```
grant insert on table_name to username;
```

여러개 권한을 한번에 줄 수도 있다. 

```
grant select,insert,update on table_name to username;
```


</br>

다음부턴...스키마랑 유저 권한 잘 접속했는지 꼭 확인해보기!!!!

