# 어플리케이션 테스트 Junit

백기선님의 [더 자바, 어플리케이션을 테스트하는 다양한 방법](https://www.inflearn.com/course/lecture?courseSlug=the-java-application-test) 을 듣고 정리한 내용입니다.

## 테스트 코드를 작성하는 이유

blabla~~~



<br>

## Junit 이란

<img width="630" alt="image" src="https://github.com/yurim022/Today-I-Learn/assets/45115557/26047a4c-7e8e-49b1-a70a-3ca432e3ff9f">

* 자바 개발자가 가장 많이 사용하는 테스팅 프레임워크
* Java8 이상 지원
* Platform:  테스트를 실행해주는 런처 제공. TestEngine API 제공.
* Jupiter: TestEngine API 구현체로 JUnit 5를 제공.
* Vintage: TestEngine API 구현체로 JUnit 4와 Junit3 제공.

<br>

## Junit 기본 사용법

#### Maven 추가

```
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-engine</artifactId>
    <version>5.5.2</version>
    <scope>test</scope>
</dependency>

```

#### Test Class 생성
   
<img width="564" alt="image" src="https://github.com/yurim022/Today-I-Learn/assets/45115557/e8c33d74-e7af-4bbc-843a-ef4d6f545e9c">
 
 테스트를 생성하기 원하는 클래스에 가서 `cmd + shift + t`를 누르면 아래와 같이 테스트 생성 가능
 
 <br>
 
 <img width="918" alt="image" src="https://github.com/yurim022/Today-I-Learn/assets/45115557/9cd44432-8b72-4ebe-91f4-f83c5eb1cb7c">

<br>

#### 기본 어노테이션

* @Test
테스트를 수행하고자 하는 함수 위에 붙이는 어노테이션.
해당 어노테이션이 붙은 단위로 테스트 실행 가능

* @BeforeAll
 