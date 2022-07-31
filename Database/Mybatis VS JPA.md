정확하게 말하자면 Mybatis와 JPA는 데이터베이스는 아니고, Java를 이용해 DB를 사용하기 위해 JDBC 라이브러리를 더 편리하게 사용하기 위한 툴이다. 

# MyBatis

SQLMapper로, 개발자가 Native SQL 코드를 작성하고 결과를 객체와 매핑하는 것까지 직접 처리한다. 

```
Controller -> Service -> Mapper(인터페이스) -> xml
```

### 장점

* SQL을 잘 알면 최적화된 코드를 작성할 수 있다. 
* 복잡한 JDBC 코드를 걷어내 깔끔한 코드를 유지할 수 있다. (DB connection 등 알아서 처리해줌)
* xml만 변경할 경우, 별도 컴파일을 하지 않아도 된다. 

### 단점

* DTO, 테이블, 인터페이스 등 변경시 변경해야될 양이 많다. 
* 서비스 로직과 쿼리를 분리하기 어렵다.  



# JPA

객체지향적으로 DB에 접근하기 위해 고안된 Hibernate 구현체이다. 
![image](https://user-images.githubusercontent.com/45115557/182015769-724239c1-d8fd-4e35-8fe2-d62029c3e629.png)


```
Controller -> Service -> Respository(JPARepository 상속)
```

SpringDataJpa는 JPA를 한단계 추상화시킨 Repository 인터페이스를 제공함으로써 EntityManger가 수행하는 코드를 개발자가 신경쓰지 않아도 되게 추상화 했다. 

![image](https://user-images.githubusercontent.com/45115557/182015847-5f8b8856-1a1f-41f8-8d07-5a8c50ec08ff.png)

위 사진을 보면 entityManager가 em.persist(), em.flush() 등을 통해 영속성 관리를 하는 엔티티 생명주기를 볼 수 있다. SpringDataJpa를 사용하면 이를 알아서 해준다. 


### 장점

* 쿼리를 추상화 함으로써 테이블이나 DB가 변경되어도 개발자가 별도 쿼리코드를 수정하지 않아도 된다. (인터페이스로만 구현할 경우)
* 개발자가 직접 코드를 작성하지 않아도 된다.
* 지연로딩, 영속 컨텍스트 캐싱, 변경 감지로 성능상이 이점이 있다. 

### 단점

* 로직이 복잡할수록 학습곡선이 높아진다. 
* 매핑 설계를 잘못하면 성능저하가 발생할 수 있다. 
* N+1, fetch join등 신경쓸 점이 많다. 


참고 링크:
https://velog.io/@dbsrud11/Spring-Data-JPA-VS-MyBatis
https://sm-studymemo.tistory.com/95
https://data-make.tistory.com/609
