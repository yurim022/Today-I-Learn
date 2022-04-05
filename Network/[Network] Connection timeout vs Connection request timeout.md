

spring에서 http관련 설정을 하는 부분이 있었다.

## application.yaml

```
http-pool:
  max-per-route: 128
  max-total: 512
  connection-request-timeout: 10000
  connect-timeout: 10000
  read-timeout: 15000
```

## Config파일

```
@Configuration
@ConfigurationProperties(prefix = "http-pool")
@Data
public class RestTemplateConfig {

    private Integer maxPerRoute;
    private Integer maxTotal;
    private Integer connectionRequestTimeout;
    private Integer connectTimeout;
    private Integer readTimeout;
    
    }
```

application.yaml에 설정되어 있는 값을 @ConfigurationProperties(prefix = "http-pool") 을 통해서 가져올 수 있다. 
그런데, connection-timeout, connection-request-timeout의 차이점이 뭘까?



## Connection Timeout

클라이언트가 서버와 커넥션을 맺을 때까지의 timeout이다. 즉, 3-way handshake까지 걸리는 시간.
보통 여기에서 timeout이 발생하면 요청받은 서버가 정상이 아닐 경우가 많다. 


## Connection Request Timeout

connection pool로부터 connection을 대여하기까지의 timeout이다. connection pool에 connection을 요청한 뒤, 일정 시간이 지나도 
connection을 받지 못했을 경우에 해당 timeout이 발생한다. 

connection pool 설정에는 다음과 같은 것들이 있다. 
- max total connection : connection pool이 가질 수 있는 최대 connection 수
- max route : (connection pool이 관리하는 connection 중) 동일한 endpoint로 동시에 요청할 수 있는 최대 connection 수

1. max total connection, max route를 너무 적게 설정한 경우 -> 단기간에 많은 요청이 올 경우
2. 타겟 서버의 데이터 처리 시간이 길게 소요되거나, 큰 크기의 파일을 다운로드 받는 경우 -> connection 수 더 크게
3. response 자원을 해제하지 않아서 connection pool에 반납되지 못하는 경우. 




참고링크:
https://velog.io/@blxckdog7702/Connection-timeout-vs-Connection-request-timeout-vs-Socket-timeout
