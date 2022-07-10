## Collection 조회 성능 최적화

**테이블 관계**

![image](https://user-images.githubusercontent.com/45115557/178133007-1413103b-54ae-465d-b903-618234dd6961.png)


**Order.class**

```java
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

**Order - OrderItems @OneToMany 관계**

```java
 @OneToMany(mappedBy = "order", cascade = CascadeType.ALL) //persist order하면 저절로 등록.삭제
    private List<OrderItem> orderItems = new ArrayList<>();
```

### V2: 엔티티를 DTO로 변환

(V1은 엔티티를 직접 노출, 엔티티가 변하면 API 스펙이 변함 + 양방향 연관관계 문제로 생략)

**OrderApiController.class**

```java
@GetMapping("/api/v2/orders")
    public List<OrderDto> ordersV2(){
        List<Order> orders = orderRepository.findAllByString(new OrderSearch());
        return orders.stream()
                .map(o -> new OrderDto(o))
                .collect(toList());
    }
```

```java
@Data
    static class OrderDto {
        private Long orderId;
        private String name;
        private LocalDateTime orderDate;
        private OrderStatus orderStatus;
        private Address address;
        private List<OrderItemDto> orderItems;

        public OrderDto(Order order) {
            orderId = order.getId();
            name = order.getMember().getName();
            orderDate = order.getOrderDate();
            orderStatus = order.getStatus();
            address = order.getDelivery().getAddress();
            orderItems = order.getOrderItems().stream()
                    .map(orderItem -> new OrderItemDto(orderItem))
            .collect(toList());
        }
```

```java
     @Data
        static class OrderItemDto {

            private String itemName;
            private int orderPrice;
            private int count;

            public OrderItemDto(OrderItem orderItem) {
                itemName = orderItem.getItem().getName();
                orderPrice = orderItem.getOrderPrice();
                count = orderItem.getCount();
            }
        }
```

- Dto를 두개 만들어야 함
- 지연로딩으로 너무 많은 SQL실행
    - order 1번
    - member, address N번 (order 조회 수 만큼)
    - orderItem N번(order 조회 수 만큼)
    - item N번(orderItem 조회 수 만큼)
- 지연로딩은 영속성 컨텍스에 있으면 SQL을 날리지 않음

**조회할 테이블 상태**

![image](https://user-images.githubusercontent.com/45115557/178133038-38b5708e-836c-402d-8e87-f24eaabbea1a.png)

![image](https://user-images.githubusercontent.com/45115557/178133045-d0bfc489-b72e-48b3-9526-83a3c89ffbb6.png)


4개의 order 존재, 각 order 당 2개의 아이템 주문 (총 8개의 아이템)

**조회 결과**

```json
[
    {
        "orderId": 4,
        "name": "userA",
        "orderDate": "2021-02-20T15:32:12.852436",
        "orderStatus": "ORDER",
        "address": {
            "city": "서울",
            "street": "1",
            "zipzode": "1111"
        },
        "orderItems": [
            {
                "itemName": "JPA! BOOK",
                "orderPrice": 10000,
                "count": 1
            },
            {
                "itemName": "JPA2 BOOK",
                "orderPrice": 20000,
                "count": 2
            }
        ]
    },
    {
        "orderId": 11,
        "name": "userB",
        "orderDate": "2021-02-20T15:32:12.879768",
        "orderStatus": "ORDER",
        "address": {
            "city": "진주",
            "street": "2",
            "zipzode": "2222"
        },
        "orderItems": [
            {
                "itemName": "SPRING BOOK",
                "orderPrice": 20000,
                "count": 3
            },
            {
                "itemName": "SPRING@ BOOK",
                "orderPrice": 40000,
                "count": 4
            }
        ]
    },
{
        "orderId": 36,
        "name": "userA",
        "orderDate": "2021-02-20T21:47:28.825684",
        "orderStatus": "ORDER",
        "address": {
            "city": "서울",
            "street": "1",
            "zipzode": "1111"
        },
        "orderItems": [
            {
                "itemName": "JPA! BOOK",
                "orderPrice": 10000,
                "count": 1
            },
            {
                "itemName": "JPA2 BOOK",
                "orderPrice": 20000,
                "count": 2
            }
        ]
    },
    {
        "orderId": 43,
        "name": "userB",
        "orderDate": "2021-02-20T21:47:28.853796",
        "orderStatus": "ORDER",
        "address": {
            "city": "진주",
            "street": "2",
            "zipzode": "2222"
        },
        "orderItems": [
            {
                "itemName": "SPRING BOOK",
                "orderPrice": 20000,
                "count": 3
            },
            {
                "itemName": "SPRING@ BOOK",
                "orderPrice": 40000,
                "count": 4
            }
        ]
    }
]
```

- order 1번

![image](https://user-images.githubusercontent.com/45115557/178133058-d793788b-f812-4885-9336-f9cc4bd76d9b.png)


- member 4번

![image](https://user-images.githubusercontent.com/45115557/178133064-cb83c606-0018-4b4d-ba91-1abfe35a9ee9.png)


- delivery 4번

![image](https://user-images.githubusercontent.com/45115557/178133072-093e9816-ce92-4bd9-b8e6-a784045ac456.png)

- orderItem 4번

![image](https://user-images.githubusercontent.com/45115557/178133078-3cff20dd-5bf5-42b8-bb67-953f78380a9b.png)


- Items (2번 x 4번) 8번

![image](https://user-images.githubusercontent.com/45115557/178133086-296e3843-b3ac-4e19-9ce8-3584b7ed55c6.png)


### V3 : 엔티티를 DTO로 변환 - 페치 조인 최적화

**OrderApiController.class**

```java
@GetMapping("/api/v3/orders")
    public List<OrderDto> ordersV3(){
        List<Order> orders = orderRepository.findAllWithItem();
        return orders.stream()
                .map(o-> new OrderDto(o))
                .collect(toList());
    }
```

**OrderRepository.class**

```java
public List<Order> findAllWithItem() {
        return em.createQuery(
                "select distinct o from Order o"+
                        " join fetch o.member m"+
                        " join fetch o.delivery d"+
                        " join fetch o.orderItems oi"+
                        " join fetch oi.item i",Order.class)
                .getResultList();
    }
```

- 1번의 SQL 쿼리

![image](https://user-images.githubusercontent.com/45115557/178133091-7685bbdf-d9ae-4c06-b64f-743d44f2e675.png)

![image](https://user-images.githubusercontent.com/45115557/178133094-6074328c-0b32-4c6d-8044-954516aec60b.png)


- 쿼리 결과 1대 다 조인으로 데이터 베이스 row 수가 증가 (orderId 중복)

![image](https://user-images.githubusercontent.com/45115557/178133105-e4a34771-1325-4c70-bdea-bfa543137f77.png)

- JPA의 distinct는 SQL에 distinct를 추가해주는 것 뿐만 아니라, 같은 엔티티가 조회되면 애플리케이션에서 중복을 걸러준다.
- 컬렉션 fetch join 사용시 페이징이 불가해진다.
- 컬렉션 fetch join 은 컬렉션 둘 이상에 사용 시, 데이터가 부정합하게 조회 될 수 있다.

### V3.1 : 엔티티를 DTO로 변환 - 페이징과 한계 돌파

컬렉션을 페치 조인하면 일대다 조인이 발생하므로 데이터가 예측할 수 없이 증가한다.

일대다에서 일(1)을 기준으로 페이징을 하는 것이 목적인데, 데이터는 다(N)를 기준으로 생성된다 .

위 예시에선 Order를 기준으로 페이징 하고 싶은데, 다(N)인 OrderItem이 페이징 기준이 되어버린다. 

**해결방법**

1. ToOne(One To One, Many To One) 관계를 모두 페치조인한다. 이들은 row 수를 증가시키지 않으므로 페이징 쿼리에 영향을 주지 않는다. 
2. 컬렉션은 지연 로딩으로 조회한다. 
3. 지연 로딩 성능 최적화를 위해 hibernate.default_batch_fetch_size , @BatchSize 를 적용한다. 
    - hibernate.default_batch_fetch_size: 글로벌 설정
    - @BatchSize: 개별 최적화
    - 이 옵션을 사용하면 컬렉션이나 프록시 객체를 한꺼번에 설정한 size만큼 IN 쿼리로 조회한다.
    
    **OrderApiController.class**
    
    ```java
    @GetMapping("/api/v3.1/orders")
        public List<OrderDto> ordersV3_page(@RequestParam(value = "offset", defaultValue = "0") int offset,
                                            @RequestParam(value = "limit",defaultValue = "100") int limit){
            List<Order> orders = orderRepository.findAllWithMemberDelivery(offset, limit);
            return orders.stream()
                    .map(o-> new OrderDto(o))
                    .collect(toList());
        }
    ```
    
    **OrderRepository.class**
    
    ```java
    public List<Order> findAllWithMemberDelivery(int offset, int limit){
            return em.createQuery(
                    "select o from Order o"+
                            " join fetch o.member m"+
                            " join fetch o.delivery d", Order.class)
                    .setFirstResult(offset) //페이징 시작
                    .setMaxResults(limit)//몇 개 데이터까지 가져올 것인
                    .getResultList();
        }
    ```
    
- offset & limit을 통한 쿼리 가능
    
![image](https://user-images.githubusercontent.com/45115557/178133124-5f08cfe0-ffa7-4cbe-bd99-e79c1171e217.png)


    **최적화 옵션 application.yml**
    
    ```java
    spring:
      jpa:properties:
        hibernate:
          default_batch_fetch_size: 1000
    ```
    
    - 쿼리 호출수가 1+N → 1+1 로 최적화 된다 (각각 1번씩 호출)
    
   ![image](https://user-images.githubusercontent.com/45115557/178133131-0acfd952-47b1-42cd-a9a9-c60f368ef9b6.png)

   
    ToOne 관계 먼저 join 및 쿼리 
    
    limit, offset이 쿼리에 추가된 것을 볼 수 있다. 
   
![image](https://user-images.githubusercontent.com/45115557/178133145-84244375-369c-45b7-8cea-b0925c292464.png)

![image](https://user-images.githubusercontent.com/45115557/178133156-38dc4776-d1c9-4f6d-8f5f-9b8d1cd9970f.png)

   
    in 으로 한꺼번에 batch_fetch_size 까지 가져온다. 
    
    - 페치 조인 방식과 비교해서 쿼리 호출 수가 약간 증가하지만, DB 데이터 전송량이 감소한다
    - 컬렉션 페치 조인은 페이징이 불가능 하지만 이 방법은 페이징이 가능하다.
    - 조인보다 DB 데이터 전송량이 최적화 된다.
    
    (Order와 OrderItem을 조인하면 Order가
    OrderItem 만큼 중복해서 조회된다. 이 방법은 각각 조회하므로 전송해야할 중복 데이터가 없다.)
    
    - default_batch_fetch_size 는 100~1000 사이를 권장
    
    이 전략은 위에서 봤듯이 SQL IN 절을 사용하는데, 데이터베이스에 따라 IN 절 파라미터를 1000으로 제한하기도 한다. 1000으로 잡으면 한번에 1000개를 DB에서 애플리케이션에 불러오므로 DB에 순간 부하가 증가할 수 있다. 하지만 애플리케이션은 100이든 1000이든 결국 전체 데이터를 로딩해야 하므로 메모리 사용량이 같다. 1000으로 설정하는 것이 성능상 가장 좋지만, 결국 DB든 애플리케이션이든 순간 부하를 어디까지 견딜 수 있는지로 결정하면 된다.
    
    ### V4 : JPA에서 DTO 직접 조회
    
    리포지토리에서 쿼리시 반환 타입을 생성한 DTO 객체로 받는다. 
    
    - 단건 조회에서 많이 사용하는 방식
    
    **OrderApiController.class**
    
    ```java
    @GetMapping("/api/v4/orders")
        public List<OrderQueryDto> ordersV4(){
            return orderQueryRepository.findOrderQueryDtos();
        }
    ```
    
    **OrderQueryRepository.class**
    
    ```java
    @Repository
    @RequiredArgsConstructor
    public class OrderQueryRepository {
    
        public List<OrderQueryDto> findOrderQueryDtos(){
            //루트 조회(toONe 코드 한번에 조회)
            List<OrderQueryDto> result = findOrders();
    
            //루프를 돌면서 컬렉션 추가( 추가 쿼리 실행)
            result.forEach(o -> {
                List<OrderItemQueryDto> orderItems = findOrderItems(o.getOrderId());
                o.setOrderItems(orderItems);
            });
            return result;
        }
    ```
    
    - 1:N 관계(컬렉션)를 제외한 나머지를 한번에 조회
    
    ```java
     private List<OrderQueryDto> findOrders() {
            return em.createQuery(
                    "select new  jpabook.jpashop.repository.order.query.OrderQueryDto(o.id, m.name, o.orderDate,\n" +
                            "o.status, d.address)"+
                            " from Order o"+
                            " join o.member m"+
                            " join o.delivery d", OrderQueryDto.class)
                    .getResultList();
        }
    ```
    
    ```java
        private List<OrderItemQueryDto> findOrderItems(Long orderId){
            return em.createQuery(
                    "select new jpabook.jpashop.repository.order.query.OrderItemQueryDto(oi.order.id, i.name," +
                            "oi.orderPrice, oi.count)"+
                            " from OrderItem oi"+
                            " join oi.item i"+
                            " where oi.order.id = : orderId",
                    OrderItemQueryDto.class)
                    .setParameter("orderId",orderId)
                    .getResultList();
        }
    ```
    
    **OrderQueryDto.class**
    
    ```java
    @Data
    @EqualsAndHashCode(of = "orderId")
    public class OrderQueryDto {
    
        private Long orderId;
        private String name;
        private LocalDateTime orderDate;
        private OrderStatus orderStatus;
        private Address address;
        private List<OrderItemQueryDto> orderItems;
    
        public OrderQueryDto(Long orderId, String name, LocalDateTime orderDate, OrderStatus orderStatus,Address address){
            this.orderId = orderId;
            this.name = name;
            this.orderDate = orderDate;
            this.address = address;
            this.orderStatus = orderStatus;
        }
    
    }
    ```
    
    **OrderItemQueryDto.class**
    
    ```java
    @Data
    public class OrderItemQueryDto {
    
        @JsonIgnore
        private Long orderId;
        private String itemName;//상품명
        private int orderPrice;//주문갸격
        private int count;//주문수량
    
        public OrderItemQueryDto(Long orderId,String itemName, int orderPrice, int count){
            this.count = count;
            this.orderId = orderId;
            this.itemName = itemName;
            this.orderPrice = orderPrice;
        }
    }
    ```
    
    **조회 결과**
    
    ```json
    {
        "orderId": 4,
        "name": "userA",
        "orderDate": "2021-02-20T15:32:12.852436",
        "orderStatus": "ORDER",
        "address": {
          "city": "서울",
          "street": "1",
          "zipzode": "1111"
        },
        "orderItems": [
          {
            "itemName": "JPA! BOOK",
            "orderPrice": 10000,
            "count": 1
          },
          {
            "itemName": "JPA2 BOOK",
            "orderPrice": 20000,
            "count": 2
          }
        ]
      },
    ```
    
    (원래 4개인데 길어서 생략)
    
    **쿼리결과**
    
    Order 쿼리 1번
    
 ![image](https://user-images.githubusercontent.com/45115557/178133180-3f9bb89a-bdac-4af2-a792-fe0869588ac6.png)

  
    orderItem - Item join하여 Order 개수만큼 쿼리 (여기선 4번)
    
 ![image](https://user-images.githubusercontent.com/45115557/178133185-a7ad2720-72d0-451a-8698-2a28a197e180.png)
   

- Query: 루트 1번, 컬렉션 N 번
- ToOne(N:1, 1:1) 관계들을 먼저 조회하고, ToMany(1:N) 관계는 각각 별도로 처리한다.
    - ToOne 관계는 조인해도 데이터 row 수 동일
    - ToMany(1:N) 관계는 조인하면 row 수가 증가
    
    ### V5: JPA에서 DTO 직접 조회 - 컬렉션 조회 최적화
    
    **OrderApiController.class**
    
    ```java
    @GetMapping("/api/v5/orders")
        public List<OrderQueryDto> ordersV5(){
            return orderQueryRepository.findAllByDto_optimization();
        }
    ```
    
    **OrderQueryRepository.class**
    
    ```java
    public List<OrderQueryDto> findAllByDto_optimization(){
    //루트 조회(toOne 코드 한번에 조회)
            List<OrderQueryDto> result = findOrders();
    
            //orderItem 컬렉션을 MAP 한방에 조회
            Map<Long,List<OrderItemQueryDto>> orderItemMap = findOrderItemMap(toOrderIds(result));
    
            //루프를 돌면서 컬렉션 추가 (추가 쿼리 실행 놉)
            result.forEach(o -> o.setOrderItems(orderItemMap.get(o.getOrderId())));
            return result;
        }
    
        private List<Long> toOrderIds(List<OrderQueryDto> result){
            return result.stream()
                    .map(o -> o.getOrderId())
                    .collect(Collectors.toList());
        }
    
        private Map<Long, List<OrderItemQueryDto>> findOrderItemMap(List<Long> orderIds){
            List<OrderItemQueryDto> orderItems = em.createQuery(
                    "select new jpabook.jpashop.repository.order.query.OrderItemQueryDto(oi.order.id, i.name," +
                            "oi.orderPrice, oi.count)"+
                            " from OrderItem oi"+
                            " join oi.item i"+
                            " where oi.order.id in :orderIds", OrderItemQueryDto.class)
                    .setParameter("orderIds",orderIds)
                    .getResultList();
    
            return orderItems.stream()
                    .collect(Collectors.groupingBy(OrderItemQueryDto::getOrderId));
        }
    ```
    
    - Query: 루트 1번, 컬렉션 1 번
    
    Order 1번
    
![image](https://user-images.githubusercontent.com/45115557/178133200-12882c70-d616-4224-bc1a-aa6c94e24602.png)


    Collection 1번
    
![image](https://user-images.githubusercontent.com/45115557/178133209-17fa36e7-a6e0-48b0-bd6c-2d186cc03899.png)


    - ToOne 관계들을 먼저 조회하고, 여기서 얻은 식별자 orderId로 ToMany 관계인 OrderItem 을
    한꺼번에 조회
    - MAP을 사용해서 매칭 성능 향상(O(1))
    
    ### 권장 순서
    
    1. **엔티티 조회 방식으로 우선 접근**
        - 페치조인으로 쿼리 수를 최적화
        
    
    2. **컬렉션 최적화**
    
    - 페이징 필요 hibernate.default_batch_fetch_size , @BatchSize 로 최적화
    - 페이징 필요X→ 페치 조인 사용
    
    3. **엔티티 조회 방식으로 해결이 안되면 DTO 조회 방식 사용**
    
    4. **DTO 조회 방식으로 해결이 안되면 NativeSQL or 스프링 JdbcTemplate**
    
- 엔티티 조회 방식은 페치 조인이나, hibernate.default_batch_fetch_size , @BatchSize 같이
코드를 거의 수정하지 않고, 옵션만 약간 변경해서, 다양한 성능 최적화를 시도할 수 있다.
- 보통 성능 최적화는 단순한 코드를 복잡한 코드로 몰고간다.
엔티티 조회 방식은 JPA가 많은 부분을 최적화 해주기 때문에, 단순한 코드를 유지하면서, 성능을 최적화 할 수 있다.
- 반면에 DTO를 직접 조회하는 방식은 성능을 최적화 하거나 성능 최적화 방식을 변경할 때 많은 코드를 변경해야 한다.
- DTO 조회 방식은 SQL을 직접 다루는 것과 유사하다. (코드변경이 많다)

- TIP ) 캐싱 시에 엔티티를 캐시에 올리면 문제발생 가능, DTO를 캐시에 올리자.

### DTO 조회 방식의 선택지

- V4는 코드가 단순하고 유지보수가 쉽다. 단건 조회 시 유용하다.

ex) 조회한 Order 데이터가 1건이면 OrderItem을 찾기 위한 쿼리도 1번만 실행하면 된다.

- V5는 코드가 복잡하다. 여러 주문을 한꺼번에 조회하는 경우에는 V5 방식을 사용해야 한다.

ex) Order 데이터가 1000건인데, V4 방식을 그대로 사용하면, 쿼리가 총 1 + 1000번 실행된다. V5
방식으로 최적화 하면 쿼리가 총 1 + 1번만 실행된다. 상황에 따라 다르겠지만 운영 환경에서 100배
이상의 성능 차이가 날 수 있다.



참고: 김영한님 강의 + 본인 세미나 자료
