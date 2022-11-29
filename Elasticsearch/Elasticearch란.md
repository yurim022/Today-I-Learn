# Elasticsearch란

회사에서 채팅서버를 개발하면서 메세지 정보를 elasticsearch에서 검색할 수 있도록 하는 작업을 진행했다. 
진행에 앞서 elasticsearch의 구조를 공부해 보자!

Elasticsearch는 검색을 위해 단독으로 사용되기도 하며 ELK(Elasticsearch / Logstash / Kibana)스택으로 사용되기도 한다. 

![image](https://user-images.githubusercontent.com/45115557/204536678-8e17b4a2-0332-4183-958c-ac673be76c35.png)


* **Logstash**

다양한 소스(DB. csv파일 등)의 로그 또는 트랜잭션 데이터를 수집,집계,파싱하여 elasticsearch로 전달

* **Elasicsearch**

Logstash로부터 받은 데이터를 검색 및 집계하여 필요한 관심 정보 획득

* **Kibana**

Elasticsearch의 빠른 검색을 통해 데이터를 시각화 및 모니터링

</br></br>

## RDBMS와 비교

![image](https://user-images.githubusercontent.com/45115557/204537397-3d0c6f4d-8e4b-4c02-a5f9-a47e015a7682.png)

검색을 하기 위해선 데이터가 저장되어야 한다. 때문에 엘라스틱서치도 데이터를 저장하게 되는데, 관계형 데이터베이스와 비교하면 다음과 같이 매핑된다.    
Index가 데이터베이스, Type이 테이블, _id가 primarykey라고 보면 되고 _id가 없다면 내부적으로 uuid를 만들어준다. 

</br></br>

## 역색인

elasticsearch는 왜 빠를까? 이유는 inverted index(역색인)이다. 
엘라스틱서치는 텍스트를 파싱해서 검색어 사전을 만든 다음, inverted index방식으로 텍스트를 저장한다. 때문에 RDBMS보다 전문검색(Full Text Search)에 빠른 성능을 보인다. 대신 Join이 불가능하고 수정/삭제 연산에 느리다. 

![image](https://user-images.githubusercontent.com/45115557/204538519-2661fde4-4729-4b30-811b-7f67bada808f.png)

</br></br>

## 특징

* **Scale out**

를 통해 규모가 수평적으로 늘어날 수 있음

* **고가용성**

Replica를 통해 데이터 안정성 보장

* **Schema Free**

Json문서를 통해 데이터 검색을 수행하므로 스키마 개념이 없음

* **Restful**

데이터 CRUD 작업은 HTTP RestfulAPI를 통해 수행하며, 다음과 같이 대응됨

![image](https://user-images.githubusercontent.com/45115557/204546227-fd344fe9-c8e4-40b4-ac10-94a67f0bf6ea.png)



 
출처:   
https://www.slideshare.net/kjmorc/ss-80803233   
https://victorydntmd.tistory.com/308   
https://esbook.kimjmin.net/03-cluster/3.1-cluster-settings
