토스의 [toss.im/slash-21](http://toss.im/slash-21) 세미나를 듣고 정리한 내용입니다.

## API 설계 기준

가맹점에게 제공할 결제API를 디자인하면서 중요하게 생각했던 세가지

- 고객(가맹점, API 사용자)의 편의
- 쉽고 간결한 디자인

## RESTful 디자인 vs 고객의 편의

**REST 정의**

자원(resource)의 표현(representation) 에 의한 상태 전달

- HTTP URI로 자원을 명시하고,
- HTTP Method(POST,  GET, PUT, DELETE)를 통해 자원에 대한 CRUD를 적용

"RESTful" 한 API 는 URI에 동작이 포함되지 않고 리소스로 명시하고, 동작은 HTTP method로 표현하는 것이다.

**NOT RESTful 한 예시** :

```jsx
 POST /datasource/read
```

method는 POST인데 동작은 데이터소스를 읽어오는 API 이다. 직관적이지 않고, 역할이 명확하지 않다. 

**RESTful 한 예시 :**

```jsx
GET /datasource/{datasourceID}
```

이렇게 RESTful 한 API 설계는 명료하지만, 실제로 API 개발을 하다보면 모든 API를 RESTful하게 짜기는 힘들다.

**DELETE, PUT 사용불가 이슈**

TOSS에서는 DELETE, PUT을 가맹점이 사용할 수 없는 문제점에 직면했다. HTTP 클라이언트 모듈이 DELETE, PUT을 미지원하거나 가맹점 인프라, 방화벽 문제로 해당 HTTP method 사용이 불가했다. 

**명사형 리소스 하위에 동사**

결제 취소 API 

```jsx
POST /payments/{key} ????
```

 POST 를 통해 DELETE를 처리해주어야 했기 때문에, 동사형 단어를 사용하였다. 

```jsx
POST /payments/{key}/cancel
```

다만, 명사형 리소스 하위에 사용함으로써 예측 가능한 일관성을 제공하였다. 

**일관성 있는 Path 구조**

```jsx
https://api.tosspayments.com/v1/payments
```

version + Resource

```jsx
https://api.tosspayments.com/v1/payments/qXJxNKg...
```

version + Resource (+ID)

```jsx
https://api.tosspayments.com/v1/payments/qXJxNKg.../cancel
```

version + Resource (+ID + Action)

 리소스를 특정하는 고유한 ID가 아닌 경우에는 

- GET query param

```jsx
?startDate=2021-01-01&
endDate=2021-01-30
```

- POST JSON 필드

```jsx
{
	"startDate": "2021-01-01"
	 "endDate": "2021-01-31"
}
```

## 인지하기 쉬운 요청과 응답

### nested Object 묶어서 모듈

1

```jsx
{
	"paymentKey": "qXJxNkgDKz0EP59Ly",
	"cardCompany": "현"
	,
	"cardNumber": "0000*******000",
	"cashReceiptNumber": "30200001",
	"cashReceiptType": "소득공제"
}
```

2

```jsx
"paymentKey": "qXJxNkgDKz0EP59Ly",
"card": {
		 "company": "현대"
		,
		 "number": "0000*******000"
 },
"cashReceipt": {
		 "number": "30200001",
		 "type": "소득공제"
 }
}
```

- 카드에 대한 정보와 현금영수증에 대한 정보가 다른 응답에서도 반복 사용할 수 있음
- 현금영수증은 발급될수도, 안될수도 있기 때문에 미발급시 객체를 null로 보내주면 됨. → API 사용자 입장에서 null check 처리하기 더 편함
- 반복필드를 하나로 묶어 요청과 응답을 인지하기 쉬움

### Recycle Object

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5eeee046-f670-4dda-8594-1598754c2b8d/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5eeee046-f670-4dda-8594-1598754c2b8d/Untitled.png)

- TOSS 결제 승인, 결제 조회, 결제 취소의 객체 레이아웃 모
- 비슷한 도메인의 요청/응답의 경우, 개발자가 예측하기 쉬움
- API 사용자의 코드를 줄일 수 있음

### Enumeration, Type 한국어 표현

결제수단

```jsx
'SC0010','CARD' → 카드, 계좌이체
```

은행코드

```jsx
'11','30' → '케이뱅크', '카카오뱅크'
```

현금영수증 타입

```jsx
'0','1','2' → '지출증빙', '소득공제'
```

어떤 데이터를 나타내고 있는지 파악하기 어려움

- 한국어로 ENUM, TYPE 표현

```jsx
	{
			"method": "카드"
			,
			"totalAmount": 15000,
			"balanceAmount" : 0
```

영어만 제공하는 경우에는 Accept-Language: ko, Accept-Language: en 등 HTTP Accept-Language 헤더를 통해 해결

```jsx
	{
			"method": "CARD"
			,
			"totalAmount": 15000,
			"balanceAmount" : 0
```

### Request Body Error Message 객체 재사용

```jsx
HTTP/1.1 400 Bad Request
{
	"code": "INVALID_CARD_COMPANY",
	"code": "유효하지 않음 카드사입니다."
}
```

오류 메세지를 바로 사용자에서 보여 줌으로써 errorHandling 쉽게 할 수 있도록 함.

참고링크

[https://toss.im/slash-21/sessions/1-7](https://toss.im/slash-21/sessions/1-7)

[https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html](https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html)
