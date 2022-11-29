# Elasicsearch란

회사에서 채팅서버를 개발하면서 메세지 정보를 elasticsearch에서 검색할 수 있도록 하는 작업을 진행했다. 
진행에 앞서 elasticsearch의 구조를 공부해 보자!

Elasticsearch는 검색을 위해 단독으로 사용되기도 하며 ELK(Elasticsearch / Logstash / Kibana)스택으로 사용되기도 한다. 

![image](https://user-images.githubusercontent.com/45115557/204536678-8e17b4a2-0332-4183-958c-ac673be76c35.png)


* Logstash 
다양한 소스(DB. csv파일 등)의 로그 또는 트랜잭션 데이터를 수집,집계,파싱하여 elasticsearch로 전달

* Elasicsearch
Logstash로부터 받은 데이터를 검색 및 집계하여 필요한 관심 정보 획득

* Kibana
Elasticsearch의 빠른 검색을 통해 데이터를 시각화 및 모니터링

</br>

# ElasticSearch 시스템구조














</br>
 
참고링크:   
https://victorydntmd.tistory.com/308   
https://esbook.kimjmin.net/03-cluster/3.1-cluster-settings
