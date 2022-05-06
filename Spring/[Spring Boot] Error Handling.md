application에서 에러가 발생했을 때, spring에서 어떻게 처리하는지 프로젝트에 적용한 내용을 정리해 보았다 .

프로젝트 원본이 아니라 내용이 전달될 수 있을 정도로 수정해서 작성하였다. 

## ErrorCode.enum

```jsx
@Getter
@ToString
public enum ErrorCode {

  INVALID_INPUT_VALUE(400, "E001", "Invalid Input Value"),
  ENTITY_NOT_FOUND(400, "E002", "Entity Not Found"),
  DATA_NOT_FOUND(400, "E003", "Data Not Found"),
  ILLEGAL_CHART_REQEUST_EXCEPTION(400,"C002","Illegal Chart Request")
(---중략---)
}
```

먼저 위와같이 에러코드를 정의한다. 

## ErrodResponse.class

```jsx
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@ToString
@Getter
public class ErrorResponse {
  private int status;
  private String code;
  private String message;
  }

@Builder
  public ErrorResponse(int status, String code, String message) {
    this.status = status;
    this.code = code;
    this.message = message;
  }
}
```

통일된 에러 형식 반환을 위해 ErrorResponse 클래스를 만들어준다. 

## CustomException.class

```jsx
@NoArgsConstructor
public class IllegalChartRequestExeption extends IllegalStateException{
    public IllegalChartRequestExeption(String s) {
        super(s);
    }

    public IllegalChartRequestExeption(String message, Throwable cause) {
        super(message, cause);

    }
}
```

위 예시는 차트요청에 대한 예외처리를 하는 클래스다.

IllegalStateException 등 RuntimeException 클래스를 상속받아서 custom exception 클래스를 만든다. 

## CustomExceptionHandler.class

```jsx
@ControllerAdvice
@ResponseBody
@Slf4j
public class CustomExceptionHandler {

  @ExceptionHandler(IllegalChartRequestExeption.class)
  @ResponseStatus(HttpStatus.BAD_REQUEST)
  public final ErrorResponse handleIllegalStateException(IllegalChartRequestExeption e) {
    log.warn(String.valueOf(e));
    return ErrorResponse.builder()
            .status(ErrorCode.ILLEGAL_CHART_REQEUST_EXCEPTION.getStatus())
            .code(ErrorCode.ILLEGAL_CHART_REQEUST_EXCEPTION.getCode())
            .message(e.getMessage())
            .build();
  }
}
```

### @ControllerAdvice

- 모든 @Controller 즉, 전역에서 발생할 수 있는 예외를 잡아 처리해주는 annotation이다.

### @ExceptionHandler

- @Controller, @RestController가 적용된 Bean내에서 발생하는 예외를 잡아서 하나의 메서드에서 처리
- @ControllerAdvice 밑에서만 동작
- @Service 에서는 동작하지 않고 컨트롤러 빈에서만 동작한다. (controller 에서 호출한 서비스에서도 동작하지만 서비스 단독 불가)

참고링크 :

[https://jeong-pro.tistory.com/195](https://jeong-pro.tistory.com/195)
