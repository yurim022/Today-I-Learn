# 어플리케이션 테스트 Junit

백기선님의 [더 자바, 어플리케이션을 테스트하는 다양한 방법](https://www.inflearn.com/course/lecture?courseSlug=the-java-application-test) 을 듣고 정리한 내용입니다.

## 테스트 코드를 작성하는 이유

blabla~~~



<br><br>

## Junit 이란

<img width="630" alt="image" src="https://github.com/yurim022/Today-I-Learn/assets/45115557/26047a4c-7e8e-49b1-a70a-3ca432e3ff9f">

* 자바 개발자가 가장 많이 사용하는 테스팅 프레임워크
* Java8 이상 지원
* Platform:  테스트를 실행해주는 런처 제공. TestEngine API 제공.
* Jupiter: TestEngine API 구현체로 JUnit 5를 제공.
* Vintage: TestEngine API 구현체로 JUnit 4와 Junit3 제공.

<br><br>

## Junit 기본 사용법

### Maven 추가

```
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-engine</artifactId>
    <version>5.5.2</version>
    <scope>test</scope>
</dependency>

```
<br><br>

### Test Class 생성
   
<img width="564" alt="image" src="https://github.com/yurim022/Today-I-Learn/assets/45115557/e8c33d74-e7af-4bbc-843a-ef4d6f545e9c">
 
 테스트를 생성하기 원하는 클래스에 가서 `cmd + shift + t`를 누르면 아래와 같이 테스트 생성 가능
 
 <br>
 
 <img width="918" alt="image" src="https://github.com/yurim022/Today-I-Learn/assets/45115557/9cd44432-8b72-4ebe-91f4-f83c5eb1cb7c">

<br><br>

### 기본 어노테이션

#### @Test

* 테스트를 수행하고자 하는 함수 위에 붙이는 어노테이션.
* 해당 어노테이션이 붙은 단위로 테스트 실행 가능

#### @BeforeAll / @AfterAll

* 테스트 클래스 내 테스트들을 실행하기 전/후 한번만 호출됨.   
* static 메소드를 사용 
* private 불가
* return type 이 있으면 안됨 (

#### @BeforeEach / @AfterEach

* 각 테스트를 실행하기 전/후 마다 실행됨     
* void만 가능


```java

class StudyTest {

    @Test
    void create() {
        Study study = new Study();
        assertNotNull(study);
        System.out.println("create");
    }

    @Test
    void create2() {
        Study study = new Study();
        assertNotNull(study);
        System.out.println("create 2");
    }

    @BeforeAll
    static void beforeAll() {
        System.out.println("before all");
    }

    @AfterAll
    static void afterAll() {
        System.out.println("after all");
    }

    @BeforeEach
    void beforeEach() {
        System.out.println("before each");
    }

    @AfterEach
    void afterEach() {
        System.out.println("after each");
    }

}
```

#### 실행결과

![image](https://github.com/yurim022/Today-I-Learn/assets/45115557/05187f71-1a46-49de-88c5-54ab5ea82a3e)

<br><br>

### 테스트 이름 표시

```java

    @Test
    @DisplayName("새로운 스터디를 생성한다.")
    void create_new_study() {
        Study study = new Study();
        assertNotNull(study);
        System.out.println("create");
    }

```

#### @DisplayName

위 어노테이션을 통해 테스트 이름을 명시할 수 있다. 

![image](https://github.com/yurim022/Today-I-Learn/assets/45115557/4ee3be12-b100-4960-9421-185475799622)

<br><br>

## Assertions

```java
org.junit.jupiter.api.Assertions.*
```
| 용도 | Assertion |
|---| ---|
|실제 값이 기대한 값과 같은지 확인 | assertEqulas(expected, actual) |
| 값이 null이 아닌지 확인 | assertNotNull(actual) |
| 다음 조건이 참(true)인지 확인 | assertTrue(boolean) |
| 모든 확인 구문 확인 | assertAll(executables...) |
| 예외 발생 확인 | assertThrows(expectedType, executable) |
| 특정 시간 안에 실행이 완료되는지 확인 | assertTimeout(duration, executable) |

<br><br>

#### assertEqulas

```java
    @Test
    @DisplayName("새로운 스터디를 생성한다.")
    void create_new_study() {
        Study study = new Study();
        assertEquals(StudyStatus.DRAFT, study.getStatus(), "스터디를 처음 만들면 상태값이 DRAFT여야 한다.");
        System.out.println("create");
    }
```

`assertEqulas(expected, actual)` 로 결과값이 기대한 값과 같은지 확인할 수 있고,    
추가로  `assertEqulas(expected, actual, message)` 로 실패 시 표시할 메세지도 표기할 수 있다. 

<br>

#### assertTrue

```java
    @Test
    @DisplayName("새로운 스터디를 생성한다.")
    void create_new_study() {
        Study study = new Study(-10);
        assertTrue(study.getLimit() > 0, "스터디 최대 참석 인원은 0보다 커야 한다.");
        System.out.println("create");
    }
```

<img width="1672" alt="image" src="https://github.com/yurim022/Today-I-Learn/assets/45115557/b9ef2a7a-a954-4208-bec5-c7c222a476e5">

테스트 실패 시 에러메세지가 뜨는 것을 알 수 있다. 

<br>


#### assertAll

여러개의 assert문을 사용한다면 하나의 assert문에서 에러가 발생하면 나머지 assert문은 동작할 수 없다.   
때문에 assertAll을 활용해서 다른 assert문의 성공 여부에 상관없이 테스트 할 수 있다. 

```java

    @Test
    @DisplayName("여러개의 assert문을 테스트한다.")
    void create_new_study_test_assertAll() {
        Study study = new Study(StudyStatus.ENDED,-10);

        assertAll(
                ()-> assertNotNull(study),
                () -> assertEquals(StudyStatus.DRAFT,study.getStatus(), "스터디를 처음 만들면 상태값이 DRAFT여야 한다.") ,
                () -> assertTrue(study.getLimit() > 0, "스터디 최대 참석 인원은 0보다 커야 한다.")
        );
        System.out.println("create");
    }
    
```

<br>

#### assertThrows

특정 예외를 테스트 하고싶을 땐 assertThrows를 사용할 수 있다. 

```java

    @Test
    @DisplayName("특정 exception이 발생하는지 테스트하고 싶을 때")
    void create_new_study_test_assertThrow() {

        IllegalArgumentException illegalArgumentException = assertThrows(IllegalArgumentException.class, () -> new Study(-10));
        String message = illegalArgumentException.getMessage();
        assertEquals(message,"참여제한 인원은 0보다 적을 수 없습니다.");
    }

```

발생한 예외를 받아서 기대했던 메세지와 같은지 비교해볼 수도 있다. 

<br>


#### assertTimeout

테스트하는 코드가 특정 시간안에 완료되어야 한다면, `assertTimeout`으로 시간에 대한 테스트도 할 수 있다. 

```java

    @Test
    @DisplayName("특정 시간안에 끝나야 하는 코드 테스트")
    void creat_new_study_timeout() {
        assertTimeout(Duration.ofMillis(100), ()-> {
            new Study(10);
            Thread.sleep(300);
        });
    }
    
```

<br><br>

## 테스트 반복 실행하기

#### @RepeatedTest

실행마다 랜덤값을 쓴다던가, 타이밍에 따라 달라질 수 있는 코드가 있는 경우 코드를 반복해서 테스트 할 수 있다.

```java

    @DisplayName("스터디 반복테스트")
    @RepeatedTest(value = 10, name = "{displayName}, {currentRepetition}/{totalRepetitions}")
    void repeatTest(RepetitionInfo repetitionInfo ){
        System.out.println("repeat test " + repetitionInfo.getCurrentRepetition()
        + "/" + repetitionInfo.getTotalRepetitions());
    }

```

`@RepeatedTest` 어노테이션을 통해 반복할 테스트임을 명시하고 반복할 횟수를 value로 줄 수 있다.   
함수 인자로 RepetitionInfo 를 받아 현재 반복횟수, 총 반복횟수 등의 정보를 활용할 수 있다.   

```java
name = "{displayName}, {currentRepetition}/{totalRepetitions}
```
테스트 이름을 제공하는 인자들을 활용하여 커스터마이징해서 사용하는 것도 가능하다. 

<br><br>

## 파라미터로 복수의 값 테스트

#### @ValueSource

여러가지 파라미터 값들을 줘서 테스트하고 싶은 경우, (경계값 등)  `@paramiterizedTest` 를 사용할 수 있다. 

```java

    @DisplayName("스터디 파라미터 인자로 받는 테스트")
    @ParameterizedTest(name = "{index} {displayName} message = {0}")
    @ValueSource(strings = {"날씨가","많이","더워지고","있네요."})
    @NullAndEmptySource
    void paramiterizedTest(String message) {
        System.out.println(message);
    }
```

`@ValueSource` 에 테스트하고자 하는 파라미터를 넣어주고, 마찬가지로 테스트에 표시할 이름을 커스터마이징 해줄 수 있다.    
파라미터로는 int,string,boolean,char,class 등 다양한 타입이 가능하다.

![image](https://github.com/yurim022/Today-I-Learn/assets/45115557/c9ab210a-914b-4f74-a449-ca3c154d3e90)

`@NullAndEmptySource` , `@NullSource` , `@EmptySource` 등을 붙여주면 ValueSource의 파라미터에 추가로 테스트할 null/empty 값을 넣어서 테스트 할 수 있다. 

<br>

#### @ConvertWith

클래스 타입으로 함수의 인자를 받고 싶은데, 인자가 1개일때 사용할 수 있는게 `@ConvertWith` 이다.

```java
static class StudyConverter extends SimpleArgumentConverter {
        @Override
        protected Object convert(Object source, Class<?> targetType) throws ArgumentConversionException {
            assertEquals(Study.class,targetType,"Can only convert to Study");
            return new Study(Integer.parseInt(source.toString()));
        }
    }
```

먼저 ConvertWith 어노테이션을 사용하려면, SimpleArgumentConverter 을 상속받은 static 클래스 구현체를 정의해주어야 한다.   
위 코드를 보면 convert함수를 override하여 source에서 받은 인자값을 통해 원하는 타입의 클래스로 변환해주고 있다.    

```java

    @DisplayName("Converter로 인자 받기")
    @ParameterizedTest(name = "{index} {displayName} message = {0}")
    @ValueSource(ints = {10,20,40})
    void parameterizedTestWithClassAndOneArgument(@ConvertWith(StudyConverter.class) Study study) {
        System.out.println(study.getLimit());
    }

```

`@ConvertWith(커스텀Converterclass)` 를 통해 변환된 클래스를 사용해줄 수 있다. 

<br>

#### ArgumentsAccessor

복수의 파라미터를 받고 싶을때, ArgumentsAccessor 를 사용할 수 있다. 
이때 `@CsvSource` 를 통해 여러개의 인자묶음을 전달한다. 이때 구분자는 여러가지가 있지만, 객체묶음은 ""로 객체 내 파라미터 구분은 ','를 사용하겠다.

```
    @DisplayName("ArgumentsAccessor로 파라미터 받기")
    @ParameterizedTest(name = "{index} {displayName} message = {0}")
    @CsvSource({"10, 자바 스터디", "20, 스프링"})
    void parameterizedTestWithClass(ArgumentsAccessor argumentsAccessor) {
        Study study = new Study(argumentsAccessor.getInteger(0),argumentsAccessor.getString(1));
        System.out.println(study.toString());
    }
```

테스트 함수의 인자로 ArgumentsAccessor 를 받고, @CsvSource에 테스트하고자 하는 값들을 넣어준다.   
argumentsAccessor.getInteger(INDEX) , argumentsAccessor.getString(INDEX) 로 인자값을 받아올 수 있다.

<br>

#### @AggregateWith

테스트 함수에서 클래스를 생성하지 않고, 바로 인자로 클래스를 받고 싶을때 `@AggregateWith` 를 사용할 수 있다.    

```java

    static class StudyAggregator implements ArgumentsAggregator {
        @Override
        public Object aggregateArguments(ArgumentsAccessor accessor, ParameterContext parameterContext) throws ArgumentsAggregationException {
            return new Study(accessor.getInteger(0), accessor.getString(1));
        }
    }

```

ArgumentsAggregator 의 구현체를 만들고, aggregateArguments 함수를 override 하면 되는데,   
SimpleArgumentConverter와 다르게 여러개 인자를 받을 수 있다.    
이때 aggregator class는 static이여야 하며, 변환하고자 하는 클래스 생성자는 public이여야 한다.

```java

    @DisplayName("ArgumentsAccessor로 파라미터 받기")
    @ParameterizedTest(name = "{index} {displayName} message = {0}")
    @CsvSource({"10, 자바 스터디", "20, 스프링"})
    void parameterizedTestWithClass(@AggregateWith(StudyAggregator.class) Study study) {
        System.out.println(study.toString());
    }

```

@AggregateWith(StudyAggregator.class) Study study` 처럼 테스트 함수에 @AggregateWith 어노테이션과 Aggregator 클래스를 통해 인자로 클래스를 받을 수 있다. 



