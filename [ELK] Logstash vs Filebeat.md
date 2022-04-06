# LogStash vs Filebeat

서버 배포 작업을 하면서 실행하는 파일중에 filebeat 파일이 있었다. 

무엇인고 보니, 분산되어 있는 서버의 각 로그들을 전송해주는 서비스였다. 
공부하는 김에 많이 비교되는 LogStash와 함께 정리해보자!

## Logstash

* 데이터 집계, 변환, 저장
* 실시간 파이프라인 지원 (Elasticsearch 데이터 파이프라인 주)
* 서로 다른 소스의 데이터를 탄력적으로 통합하고 사용자가 선택한 목적지로 정규화 할 수 있다. 

### 장점
* Filebeat에 비해 다양한 [input](https://www.elastic.co/guide/en/logstash/current/input-plugins.html), [output](https://www.elastic.co/guide/en/logstash/current/output-plugins.html)을 제공한다. 




## Filebeat





참고링크:
https://kanoos-stu.tistory.com/20

