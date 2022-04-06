# LogStash vs Filebeat

서버 배포 작업을 하면서 실행하는 파일중에 filebeat 파일이 있었다. 

무엇인고 보니, 분산되어 있는 서버의 각 로그들을 전송해주는 서비스였다. 
공부하는 김에 많이 비교되는 LogStash와 함께 정리해보자!

### 한줄요약: 
> Filebeat가 logstash보다 가벼워 서버에 부하가 덜 가며 확장이 용이한 반면, logstash는 데이터 변환, 집계, 저장에 이점이 있다. 


## Logstash
![image](https://user-images.githubusercontent.com/45115557/161879902-9c3a9d52-3c9f-48c1-942d-ccfb3caf9601.png)

* 데이터 집계, 변환, 저장
* 실시간 파이프라인 지원 (Elasticsearch 데이터 파이프라인 주)
* 서로 다른 소스의 데이터를 탄력적으로 통합하고 사용자가 선택한 목적지로 정규화 할 수 있다. 

### 장점
* Filebeat에 비해 다양한 [input](https://www.elastic.co/guide/en/logstash/current/input-plugins.html), [output](https://www.elastic.co/guide/en/logstash/current/output-plugins.html)을 제공한다. 
* [grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) 등 여러가지 Filter 기능을 통해 Input된 데이터를 필요한 형태로 가공이 가능하다. 

### 단점
* Filebeat에 비해 많은 리소스가 소모된다.


## Filebeat
* 여러 종류의 데이터(주로 로그)들을 서버에서 다른 곳으로 전송하는 서비스
* 로그 데이터를 전달하고 중앙하기 위한 경량 Producer
* 지정한 로그파일 또는 위치를 모니터링하고 수집해 Elasticsearch 또는 Logstash로 전달
* 별도의 분석 없이 로그를 직접 전달이나 로그가 json 으로 남는 경우에 적절


### 장점
* 리소스를 상대적으로 적게 사용한다.
* 득정 로그타입에 대해 모듈을 제공한다.
* 간단한 filter를 제공한다. 

### 단점
* Input, Output이 제한적이다. 
* [Input](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-log.html) : file의 변경 읽기
* [Output](https://www.elastic.co/guide/en/beats/filebeat/current/configuring-output.html): logstash, elasticsearch, redis, kafka 등

```
filebeat.inputs:
- type: log
  paths:
    - /var/log/messages
    - /var/log/*.log
     include_lines: ['^ERR', '^WARN']
     exclude_lines: ['^DBG']
```


참고링크:
https://kanoos-stu.tistory.com/20
https://velog.io/@deet1107/logstash-filebeat
