# JPA 기본 사용법

JPA는 JAVA ORM표준 기술로, 객체와 DB를 매핑해준다. 

마치 collection 객체를 쓰는것처럼 사용할 수 있도록 (객체지향적으로) 매핑해준다. 

**저장, 조회, 수정, 삭제 지원**

```java
jpa.persist(member)
```

```java
Member member  = jpa.find(memberId)
```

```java
member.setName("name")
```

```java
jpa.remove(member)
```

**ORM이란**

- **Object-relational mapping(객체 관계 매핑)**
- 객체는 객체대로 설계
- 관계형 데이터베이스는 관계형 데이터베이스대로 설계
- ORM 프레임워크가 중간에서 매핑
- 대중적인 언어에는 대부분 ORM 기술이 존재

**JPA 동작방법**
![image](https://user-images.githubusercontent.com/45115557/178132330-aafb5be8-092e-4bb5-83aa-a1c2416768ee.png)


**JPA 는 내부적으로 쿼리를 싸준다.** 

개발자가 할일
```
jpa.persist(album);
```

나머진 JPA가 처리
```
INSERT INTO ITEM...
INSERT INTO ALNUM...
```

album이 Item을 상속받은 객체관계를 테이블로 구현하면, 각각의 테이블이 생성되고 각각 쿼리를 해줘야 하는데 이를 대신 해준다.

**JPA 와 연관관계 객체 그래프 탐색**

연관관계 저장
```
member.setTeam(team);
jpa.persist(member);
```

객체 그래프 탐색
```
Member member = jpa.find(Member.class, memberId);
Team team = member.getTeam();
```

개발자가 직접 쿼리를 짜지 않아도, 알아서 다 해준다. 

즉, 연관관계에 있는 객체를 일일이 매핑하지 않아도 되고, 이는 신뢰성을 보장한다. 

**JPA 설정**

**방언**

- JPA는 특정 데이터베이스에 종속 X
• 각각의 데이터베이스가 제공하는 SQL 문법과 함수는 조금씩 다름
• 가변 문자: MySQL은 VARCHAR, Oracle은 VARCHAR2
• 문자열을 자르는 함수: SQL 표준은 SUBSTRING(), Oracle은
SUBSTR()
• 페이징: MySQL은 LIMIT , Oracle은 ROWNUM
방언: SQL 표준을 지키지 않는 특정 데이터베이스만의 고유한 기능

**main/resources/META-INF/persistence.xml**
```
<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.2"
             xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd">
    <persistence-unit name=" hello ">
        <properties>
            <!-- 필수 속성 -->
            <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
            <property name="javax.persistence.jdbc.user" value="sa"/>
            <property name="javax.persistence.jdbc.password" value=""/>
            <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
            <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>
///방언 dialect를 통해, mySQL, oracle 등 다른 언어에 대해서 JPA가 다 처리해준다. 
            <!-- 옵션 -->
            <property name="hibernate.show_sql" value="true"/>
            <property name="hibernate.format_sql" value="true"/>
            <property name="hibernate.use_sql_comments" value="true"/>
            <!--<property name="hibernate.hbm2ddl.auto" value="create" />-->
        </properties>
    </persistence-unit>
</persistence>
```



참고: 인프런 김영한 강사님 강의
