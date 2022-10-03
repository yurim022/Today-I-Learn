# Log Rolling

## AS -IS

![image](https://user-images.githubusercontent.com/45115557/177543587-7937a8f8-d6d6-4d89-adaf-e9b62c7a4f41.png)


ls -lh명령어로 log 파일 용량을 확인해보니 2G가 넘는다. 

application rotate 적용이 필요하다. 

현재는 배포 시에 application.log에 저장하도록 설정되어 있다. 이걸 log rotate 해서 특정 조건에 따라 파일이 나눠지도록 만들어보자. 

```yaml
nohup java -jar /root/{your-project}/target/{project-snapshot.jar} >> /root/{your-project}/logs/application.log &
```

[https://stackoverflow.com/questions/65057968/how-to-create-spring-boot-logs-in-standalone-project-deployed-on-windows](https://stackoverflow.com/questions/65057968/how-to-create-spring-boot-logs-in-standalone-project-deployed-on-windows)

- nohup
    
    nohup은 터미널이나 세션이 종료되어도 해당 프로세스가 종료되지 않고 실행될 수 있게 한다.  (no hang up)
    
    - & : 백그라운드 실행
    - nohup으로 실행시킬 파일은 반드시 755 퍼미션을 가지고 있어야 함
    - $ nohup ./my_shellscript.sh > nohup_script.out '>' 또는 '>>'를 통해 다른파일에 출력할 수 있다.
    - $ nohup ./my_shellscript.sh > /dev/null  어디에도 표준출력을 기록하고 싶지 않다면 다음과 같이 설정
    - nohup을 사용하면 기본적으로 nohup.out에 표준출력이 쌓임

## TO - BE

## Rotate 적용

Rotate 적용은 크게 두가지가 있다. 

# 1. spring config를 통해 설정

![image](https://user-images.githubusercontent.com/45115557/177543710-d9d7f1d8-98f0-4b51-a451-24091b9eefb1.png)


스프링 부트는 SLF4J 인터페이스를 사용하며, 기본적으로 Logback을 구현체로 선택하고 있다. 

### SpringBoot Log

기본적으로 Spring Boot log는 console 만 log를 처리하고 log file을 작성하지 않는다.

만약 console 출력 외에 log file을 남기길 원한다면 application.yaml을 작성한다. 

```yaml
logging:
  level:
    root: info
```

- 전역 설정이라고 볼 수 있다.
- 지역적으로 선언된 logger 설정이 있다면 해당 logger 설정이 default로 적용된다.

### Rolling 설정

```yaml
logging:
  pattern:
    rolling-file-name: iot-analytics-%d{yyyy-MM-dd}.%i.log
    file: %d [%thread] %-5level %-60logger{60} : %msg%n #로그출력 형식
  #logging.pattern.console 로 콘솔 출력 customizing 가능
  file:
    name: iot-analytics.log #logging.file.path와 동시에 사용 불가
    max-size: 2KB #default 10MB
    max-history: 30 #default 7 days
    total-size-cap: 4GB #용량 초과하면 log 파일 삭제
    clean-history-onstart : true #true 여야 total-size-cap에 맞게 용량 초과 로그 삭제

  level:
    root: info
  
```

- logging.pattern.rolling-file-name : 롤링할 파일 패턴을 지정해준다. path와 함께 지정해주면 된다.
- logging.pattern.file: 파일의 출력 포멧

![image](https://user-images.githubusercontent.com/45115557/177543781-b324cae4-4a62-4953-9a87-aeb9d6b759b1.png)

logging.patter.file 에서 사용하는 log format syntax 이다. 원하는 대로 customize 해서 사용하면 된다. 

- [logging.file.name](http://logging.file.name) : 파일의 이름, logging.file.path와 함께 사용 불가하다.
- logging.file.max-size: max-size가 넘어가면 롤링 파일 이름 패턴에 맞게 새로운 파일을 생성한다.
- logging.file.max-history: 최대 로그 저장일수 지정, 기본 7
- logging.file.total-size-cap: 전체 로그 저장 용량, 이를 넘으면 파일을 삭제한다.
- logging.file.clean-history-onstart: 이 옵션이 true 여야 용량초과되거나, 히스토리 일수를 초과한 로그를 삭제할 수 있다.

좀 더 상세한 설정을 원한다면 logback.xml 파일을 생성하자.

# 2. spring-logback.xml 작성

## spring-logback.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<configuration>
    <appender name="ROLLING_FILE"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>./logs/iot-analytics.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>./logs/iot-analytics.%d{yyyy-MM-dd}.%i.log</fileNamePattern>

            <timeBasedFileNamingAndTriggeringPolicy
                    class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <maxHistory>30</maxHistory>
            <totalSizeCap>4GB</totalSizeCap>
            <cleanHistoryOnStart>true</cleanHistoryOnStart>
        </rollingPolicy>
        <encoder>
            <pattern>%d [%thread] %-5level %-60logger{60} : %msg%n</pattern>
        </encoder>
    </appender>

    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <layout class = "ch.qos.logback.classic.PatternLayout">
            <pattern>%d [%thread] %highlight(%-5level) %-60logger{60} : %msg%n</pattern>
        </layout>
    </appender>

    <root level = "info">
        <appender-ref ref = "STDOUT" />
        <appender-ref ref = "ROLLING_FILE" />
    </root>
</configuration>
```

- resource 폴더 밑에 생성해주어야 제대로 동작한다.
- Appender는 encoder의 패턴에 맞게 로그를 콘솔, 혹은 파일에 출력하는 기능을 수행한다. 콘솔 및 파일에 맡는 패턴을 각각 설정해주고 appender-ref 에서 선언해주면 스프링 가동 시 로그가 출력된다.

## 2-1) 운영, 개발환경 별 로그 설정 및 로그 레벨에 따른 로깅 적용

- spring-logback에서 콘솔 및 파일 로그파일을 가져와서 설정해준다.
- <springProfile> 을 통해 프로파일 별 적용하기 원하는 로깅을 적용해준다.
- 각각의 프로파일에 파일을 include 하고, appender-ref로 루트 및에 appender 를 적용한다.

이렇게 하면 구조를 파악하기도 쉽고, 로그 레벨별로 찾아볼 수 있어서 편하다!!

dev환경엔 cosole 만 있으면 되니까 파일 저장 없이 콘솔 옵션만 추가하면 깔끔!

### spring-logback.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<configuration>
    <springProfile name="!prod">
        <include resource="console-appender.xml"/>

        <root level="INFO">
            <appender-ref ref="CONSOLE"/>
        </root>
    </springProfile>

    <springProfile name = "prod">
        <include resource="file-info-appender.xml"/>
        <include resource="file-warn-appender.xml"/>
        <include resource="file-error-appender.xml"/>

        <root level="INFO">
            <appender-ref ref="ROLLING_FILE_INFO"/>
            <appender-ref ref="ROLLING_FILE_WARN"/>
            <appender-ref ref="ROLLING_FILE_ERROR"/>
        </root>
    </springProfile>

</configuration>
```

### console-appender.xml

```xml
<included>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <layout class = "ch.qos.logback.classic.PatternLayout">
            <pattern>%d [%thread] %highlight(%-5level) %-60logger{60} : %msg%n</pattern>
        </layout>
    </appender>
</included>
```

### file-error-appender.xml

```xml
<included>
    <appender name="ROLLING_FILE_ERROR"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>./logs/error/iot-analytics-error.log</file>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>./logs/error/iot-analytics-error.%d{yyyy-MM-dd}.%i.log</fileNamePattern>

            <timeBasedFileNamingAndTriggeringPolicy
                    class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <maxHistory>30</maxHistory>
            <totalSizeCap>3GB</totalSizeCap>
            <cleanHistoryOnStart>true</cleanHistoryOnStart>
        </rollingPolicy>
        <encoder>
            <pattern>%d [%thread] %-5level %-60logger{60} : %msg%n</pattern>
        </encoder>
    </appender>
</included>
```

### file-warn-appender.xml

```xml
<included>
    <appender name="ROLLING_FILE_WARN"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>./logs/warn/iot-analytics-warn.log</file>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>WARN</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>/./logs/warn/iot-analytics-warn.%d{yyyy-MM-dd}.%i.log</fileNamePattern>

            <timeBasedFileNamingAndTriggeringPolicy
                    class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <maxHistory>30</maxHistory>
            <totalSizeCap>3GB</totalSizeCap>
            <cleanHistoryOnStart>true</cleanHistoryOnStart>
        </rollingPolicy>
        <encoder>
            <pattern>%d [%thread] %-5level %-60logger{60} : %msg%n</pattern>
        </encoder>
    </appender>
</included>
```

### file-info-appender.xml

```xml
<included>
    <appender name="ROLLING_FILE_INFO"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>./logs/info/iot-analytics-info.log</file>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>INFO</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>./logs/info/iot-analytics-info.%d{yyyy-MM-dd}.%i.log</fileNamePattern>

            <timeBasedFileNamingAndTriggeringPolicy
                    class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>100MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <maxHistory>30</maxHistory>
            <totalSizeCap>3GB</totalSizeCap>
            <cleanHistoryOnStart>true</cleanHistoryOnStart>
        </rollingPolicy>
        <encoder>
            <pattern>%d [%thread] %-5level %-60logger{60} : %msg%n</pattern>
        </encoder>
    </appender>
</included>
```

# Log LEVEl

* DEBUG : 개발/장애 시 내부 로직, 라이브러리 동작 확인 용도
* INFO : 정상 처리 되었으나 VOC 등 처리를 위해 남겨야 할 정보
* WARN : 아용자 에러, 사용자 요청으로 인한 에러로 사용자가 잘못 파라미터를 입력하거나 개발자는 몰라도 VOC 대응을 위해 운영자가 알아야 할 오류 발생 시
* ERROR : 시스템 에러, 사용자 요청으로 인한 에러가 아닌 개발/운영자가 알아햐 할 내부 DB, MQ 등 미들웨어 오류 발생 시

보통 로그 파일은 default INFO 부터 기록한다. Debug, Trace는 콘솔에는 출력되나 파일에는 기록되지 않는다.

# Error Handling

```java
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
```

위와 같이 CustomExceptionHandler에서 에러가 발생하면 log.warn 을 발생시키도록 설정하였다. 

### Exception e 로그 남기는 법

```java
try {
    process();
} catch (IOException e) {
    log.warn("Service Layer IOException {}", e.getMessage(), e);
    throw e
}    
    
```

* e : 로깅 시 trace 포함 출력, 에러 발생 코드 위치 등
* e.getMesssage() : 간단하게 한줄 에러 메시지 로깅
* e.toString : Exception 종류 및 간단한 에러 

참고링크

[https://joonyon.tistory.com/98](https://joonyon.tistory.com/98)

[https://goddaehee.tistory.com/206](https://goddaehee.tistory.com/206)

[https://luvstudy.tistory.com/133](https://luvstudy.tistory.com/133)

[https://mkyong.com/logging/logback-xml-example/](https://mkyong.com/logging/logback-xml-example/)

[https://sematext.com/blog/logging-levels/](https://sematext.com/blog/logging-levels/)
