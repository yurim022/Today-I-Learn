# 왜 JPA를 사용하는가?

- 관계형 DB (Oracle, MySQL..)아직 많이 사용하고 있음
![image](https://user-images.githubusercontent.com/45115557/178130877-56b5e86c-22b9-4209-9b31-a90354bf682e.png)


### 결국, 객체를 관계형 데이터베이스에 저장하고 싶다. 객체지향적으로.

### SQL 중심 코딩의 문제점

- 반복적인 CRUD 코드

```
public class Member {
private String memberId;
private String name;
}
```

```
INSERT INTO MEMBER(MEMBER_ID, NAME) VALUES
SELECT MEMBER_ID, NAME FROM MEMBER M
UPDATE MEMBER SET ...

```

- 필드 추가 시 query 수정 필요 / 수정 미 반영으로 인한 오류발생


```
public class Member {
private String memberId;
private String name;
private String tel;
}
```

```
INSERT INTO MEMBER(MEMBER_ID, NAME, TEL) VALUES
SELECT MEMBER_ID, NAME , TEAM FROM MEMBER M
UPDATE MEMBER SET ... TEL = ?
```

    
 ### 객체를 자바 컬렉션에 저장하듯이 DB에 저장하고 불러오고 싶다!!
 ```
    list.add(member);
    
    Member member = list.get(memberId);
    Team team = member.getTeam();
    
 ```
SQL로는 조회하기 위해서 team, member 를 조인하고 query 해야함. 객체와 가져오는 방식이 다르다!
    
객체는 내부참조라면, SQL은 외부참조
    
 ### 객체 그래프 탐색
    
 ![image](https://user-images.githubusercontent.com/45115557/178130977-4944e26f-e95e-4a3b-a630-87dbbd35dcd8.png)

    
객체 그래프는 이렇게 자유롭게 탐색이 가능해야 하는데....SQL은 처음 실행하는 쿼리에 따라 탐색 범위가 결정된다!
    
```
SELECT M.* T.*
FROM MEMBER M
JOIN TEAM T ON M.TEAM_ID = T.TEAM_ID
```
```
member.getTeam(); //OK
member.getOrder(); //null
```
    
→ Team은 있는데 Order는 비어있을 수 있음...Order도 또 call 해줘야되..!!!
    

**DB조회를 객체지향적으로 하려 하면 mapping을 위해 더 많은 코드가 필요..ROI가 안나온다!
그래서 JPA가 있다~~~**
