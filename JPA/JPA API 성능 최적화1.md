### 예시 데이터의 테이블 관계

주문시스템
![image](https://user-images.githubusercontent.com/45115557/178132458-9db25457-0783-4b6f-9de6-335dce79dd5c.png)


## API 설계 기본 - DTO 사용

### 회원조회 API

### V1 : 응답 값으로 엔티티를 직접 외부에 노출

MemberController.class

```java
@RestController
@RequiredArgsConstructor
public class MemberApiController {

 private final MemberService memberService;

 @GetMapping("/api/v1/members")
 public List<Member> membersV1() {
	 return memberService.findMembers();
 }
}
```

@RestController = @ResponseBody + @Controller 

    → Json 형태로 객체를 반환해주는 annotation

**응답**

![image](https://user-images.githubusercontent.com/45115557/178132472-766a155b-b506-4039-9e69-c56825be813e.png)


**문제점**

- 응답값으로 엔티티를 직접 외부에 노출
- 응답 스펙을 맞추기 위해 엔티티에 로직이 추가된다 (@JsonIgnore 등)
- 같은 엔티티에 다양한 용도의 API 가 적용되면, 한 엔티티에 각각의 API에 대한 프레젠테이션 응답 로직을 다 담기 어렵다.

ex) 어떤 API는 Address 필요없고, 어떤 API는 Address는 필요한데 orders가 필요없고

![image](https://user-images.githubusercontent.com/45115557/178132482-2a7acecb-b83e-430a-a19b-3b28f65d10c7.png)

### V2 : 응답 값으로 엔티티가 아닌 별도 DTO 사용

MemberController.class

```java
@GetMapping("/api/v2/members")
 public Result membersV2() {
	 List<Member> findMembers = memberService.findMembers();
	 //엔티티 -> DTO 변환
	 List<MemberDto> collect = findMembers.stream()
	 .map(m -> new MemberDto(m.getName()))
	 .collect(Collectors.toList());
	 return new Result(collect);
 }

 @Data
 @AllArgsConstructor
 class Result<T> {
	 private T data;
 }

 @Data
 @AllArgsConstructor
 class MemberDto {
	 private String name;
 }
```

**응답**

![image](https://user-images.githubusercontent.com/45115557/178132497-1257846f-625a-4874-98d7-b7bef03bfccb.png)


**설계방법**

- DTO를 생성하여 반환객체 MemberDto type으로 변경
- Result class를 통해 반환리스트를 그대로 주지 않고 한번 감싸서 줌 → 이후 필드 추가 가능
- 엔티티가 변해도 API 스펙이 변하지 않는다.

# API 성능향상 - 지연 로딩과 조회 성능 최적화

## **주문목록 조회 API**

**예시 데이터**

- userA - JPA1 BOOK / JPA2 BOOK
- userB - SPRING1 BOOK / SPRING2 BOOK

유저A,B가 각각 책을 2권씩 구매.  (총 2개의 order)

![image](https://user-images.githubusercontent.com/45115557/178132514-5ecb7fa6-17a5-4c74-9ed4-5022ded2157c.png)


### 주문 조회 V1: 엔티티 직접 노출

Order.class

```java
@Entity
@Table(name="orders")
@Getter @Setter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Order {

    @Id
    @GeneratedValue
    @Column(name="order_id")
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY) //다대일
    @JoinColumn(name="member_id")  //FK이름이 member_id //연관관계의 주인
    private Member member;

    @OneToMany(mappedBy = "order", cascade = CascadeType.ALL) //persist order하면 저절로 등록.삭제
    private List<OrderItem> orderItems = new ArrayList<>();

    @OneToOne(fetch = FetchType.LAZY,cascade = CascadeType.ALL)
    @JoinColumn(name = "delivery_id")
    private Delivery delivery;

    private LocalDateTime orderDate; //hibernate가 알아서 지원해줌

    @Enumerated(EnumType.STRING)
    private OrderStatus status; //주문상태 [ORDER, CANCEL]
}
```

**Order Entity**

- **Member @ManyToOne , Delivery @OneToOne 관계**
- **FetchType.Lazy 즉, order → member 와 order → address는 지연로딩**

OrderSimpleApiController.class

```java
@RestController
@RequiredArgsConstructor
public class OrderSimpleApiController {
 private final OrderRepository orderRepository;
 /**
 * V1. 엔티티 직접 노출
 * - Hibernate5Module 모듈 등록, LAZY=null 처리
 * - 양방향 관계 문제 발생 -> @JsonIgnore
 */
 @GetMapping("/api/v1/simple-orders")
 public List<Order> ordersV1() {

	 List<Order> all = orderRepository.findAll();
	 for (Order order : all) {
		 order.getMember().getName(); //Lazy 강제 초기화
		 order.getDelivery().getAddress(); //Lazy 강제 초기화
	 }
	 return all;
 }
}
```

**응답**

![image](https://user-images.githubusercontent.com/45115557/178132522-e9dbfd12-0d6d-45de-a9a5-2cb055d98380.png)


- ByteBuddyIntercepter

proxy객체로, **LAZY loading** 을 했기 때문에 아직 객체가 쿼리 및 응답되지 않은 상태에서 API response가 동작했기 때문이다.

**해결책**

Hibernate5Module 등록

```java
@Bean
Hibernate5Module hibernate5Module() {
	 return new Hibernate5Module();
}
```

- 초기화된 프록시 객체만 노출, 초기화 되지 않은 프록시 객체는 노출 안함

라이브러리 추가

```java
com.fasterxml.jackson.datatype:jackson-datatype-hibernate5
```

**응답**

![image](https://user-images.githubusercontent.com/45115557/178132561-332d8265-3691-4833-b0e5-9c32993c224a.png)


- 지연로딩된 것은 null

FORCE_LAZY_LOADING

```java
@Bean
Hibernate5Module hibernate5Module() {
	 Hibernate5Module hibernate5Module = new Hibernate5Module();
	 hibernate5Module.configure(Hibernate5Module.Feature.FORCE_LAZY_LOADING,true);
	
	 return hibernate5Module;
}
```

**응답**

![image](https://user-images.githubusercontent.com/45115557/178132582-1135b866-105c-4de2-ac67-616b67c73859.png)

→ 잘 로딩됨

- 하지만 Hibernate5Module 은 엔티티 객체를 직접 반환하는 방식이기 때문에 지양
- 지연로딩(LAZY) 를 피하기 위해 즉시 로딩(EARGR) 설정 BAD
- EARGR 설정 시 연관관계가 필요 없는 경우에도 데이터를 항상 조회해서 성능 문제 발생 가능

### 주문 조회 V2: 엔티티를 DTO로 변환

OrderSimpleApiController.class

```java
@GetMapping("/api/v2/simple-orders")
public List<SimpleOrderDto> ordersV2() {

	 List<Order> orders = orderRepository.findAll();
	 List<SimpleOrderDto> result = orders.stream()
	 .map(o -> new SimpleOrderDto(o))
	 .collect(toList());
	 return result;

}
```

```java
@Data
static class SimpleOrderDto {
	 private Long orderId;
	 private String name;
	 private LocalDateTime orderDate; //주문시간
	 private OrderStatus orderStatus;
	 private Address address;

	 public SimpleOrderDto(Order order) {

	 orderId = order.getId();
	 name = order.getMember().getName();
	 orderDate = order.getOrderDate();
	 orderStatus = order.getStatus();
	 address = order.getDelivery().getAddress();

 }
```

**응답OK**

![image](https://user-images.githubusercontent.com/45115557/178132596-1d37a5e8-f31d-49bf-937a-f6b54b16e1ba.png)


**문제점**

- 쿼리가 총 1 + N +N 번 실행
- order 조회 1번(order 조회 결과 수가 N)
- order -> member 지연 로딩 조회 N 번
- order -> delivery 지연 로딩 조회 N 번
- 이미 조회된 경우 JPA 영속성 컨텍스트 안에서 캐싱, 그러나 이런 경우는 흔치 않다고 함

order query1번 호출
![image](https://user-images.githubusercontent.com/45115557/178132606-d4197da1-ea69-47ab-bb13-78a3ae493d7d.png)


member query 2번 호출(+N)
![image](https://user-images.githubusercontent.com/45115557/178132609-e150fde6-d292-4128-aeeb-71971053ec50.png)


delivery 2번 호출(+N)
![image](https://user-images.githubusercontent.com/45115557/178132615-553294db-45de-4c2a-82c5-a402327726a2.png)



### 주문 조회 V3: 엔티티를 DTO로 변환 - 페치 조인 최적화

OrderSimpleApiController.class

```java
@GetMapping("/api/v3/simple-orders")
public List<SimpleOrderDto> ordersV3() {

 List<Order> orders = orderRepository.findAllWithMemberDelivery();
 List<SimpleOrderDto> result = orders.stream()
 .map(o -> new SimpleOrderDto(o))
 .collect(toList());
 return result;

}
```

OrderRepository.class

```java
public List<Order> findAllWithMemberDelivery() {

 return em.createQuery(
 "select o from Order o" +
 " join fetch o.member m" +
 " join fetch o.delivery d", Order.class)
 .getResultList();

}
```

→ 한 번 쿼리 실행시 다 로딩

**응답OK**

![image](https://user-images.githubusercontent.com/45115557/178132751-a2d8ff64-7030-401e-91fa-bd862a543936.png)

**쿼리 1번 호출**

![image](https://user-images.githubusercontent.com/45115557/178132756-f8f6cf16-2741-478e-9e96-67ed912d25e7.png)
![image](https://user-images.githubusercontent.com/45115557/178132761-5dc749bd-5b41-424b-965f-8460e2ed8d19.png)


### 주문 조회 V4: JPA에서 DTO로 바로 조회

OrderSimpleApiController.class

```java
@GetMapping("/api/v4/simple-orders")
public List<OrderSimpleQueryDto> ordersV4() {

	 return orderSimpleQueryRepository.findOrderDtos();
}
```

OrderSimpleQueryDto.class

```java
@Data
public class OrderSimpleQueryDto {
 private Long orderId;
 private String name;
 private LocalDateTime orderDate; //주문시간
 private OrderStatus orderStatus;
 private Address address;

 public OrderSimpleQueryDto(Long orderId, String name, LocalDateTime
orderDate, OrderStatus orderStatus, Address address) {

	 this.orderId = orderId;
	 this.name = name;
	 this.orderDate = orderDate;
	 this.orderStatus = orderStatus;
	 this.address = address;
 }
}
```

OrderSimpleQueryRepository.class

```java
@Repository
@RequiredArgsConstructor
public class OrderSimpleQueryRepository {
	 private final EntityManager em;
	 public List<OrderSimpleQueryDto> findOrderDtos() {

	 return em.createQuery(
	 "select new jpabook.jpashop.repository.order.simplequery.
	OrderSimpleQueryDto(o.id, m.name,o.orderDate, o.status, d.address)" +
	 " from Order o" +
	 " join o.member m" +
	 " join o.delivery d", OrderSimpleQueryDto.class)
	 .getResultList();

 }
}
```

- 조회 전용 리포지토리 생성
- 리포지토리에 API 규격에 의존성이 있기때문에 분리하는 것이 좋음
- new 명령어를 사용해서 JPQL의 결과를 DTO로 즉시 변환

**쿼리 1회 호출** 
![image](https://user-images.githubusercontent.com/45115557/178132779-5f205417-8aa6-404d-ba63-a21e6a3e9972.png)


**Pros**

- select 절에서 원하는 데이터를 직접 선택하므로 DB→애플리케이션 network 용량 최적화

(그러나 큰 차이는 아님)

**Cons**

- 리포지토리 재사용성 떨어짐
- API 스펙에 맞춘 코드가 리포지토리에 들어감 (리포지토리 따로 분리 권장)

### 그렇다면, 쿼리방식을 어떻게 선택해야 하나?

1. 우선 엔티티를 DTO로 변환하는 방법을 선택
2. 필요하면 페치 조인으로 성능을 최적화 한다. 대부분의 성능 이슈가 해결
3. 그래도 안되면 DTO로 직접 조회하는 방법을 사용
4. 최후의 방법은 JPA가 제공하는 네이티브 SQL이나 스프링 JDBC Template을 사용해서 SQL을 직접
사용






참고: 인프런 김영한 강사님 강의 + 본인이 스터디 세미나에서 발표한 내용~
