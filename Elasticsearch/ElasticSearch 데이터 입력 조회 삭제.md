# ElasticSearch 데이터 입력 조회 삭제

터미널에서 es 인덱스, 도큐먼트를 제어하는 방법을 알아보자.
</br>

### 도큐먼트 조회

```
curl -XGET http://localhost:9200/{index}/_doc/{document_id}
```

curl API를 사용하는 방법도 있지만, Kibana의 DevTools에서도 입력할 수 있다.  

```
GET my_index/_doc/1
```
</br>

### 도큐먼트 생성

```
curl -XPUT "http://localhost:9200/my_index/_doc/1" -H 'Content-Type: application/json' -d'
{
  "name": "Jongmin Kim",
  "message": "안녕하세요 Elasticsearch"
}'
```

파일을 넣어줘도 되고 json 자체를 넣어줘도 된다.
</br>


### 입력 (PUT)

#### 요청

```
PUT my_index/_doc/1
{
  "name":"Jongmin Kim",
  "message":"안녕하세요 Elasticsearch"
}
```

#### 응답

```
{
  "_index" : "my_index",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 2,
  "result" : "updated",
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 1,
  "_primary_term" : 1
}

```
이때 처음 생성되면 result에 created, 수정이면 updated 가 표시된다. 
</br>


### 수정 (POST)

수정은 PUT과 비슷하게 동작하나, 도큐먼트 id의 자동생성은 PUT으로는 제공되지 않는다.

#### 요청
```
POST my_index/_doc
{
  "name":"Jongmin Kim",
  "message":"안녕하세요 Elasticsearch"
}
```
</br>

### 삭제 (DELETE)

#### 요청
```
DELETE my_index/_doc/1
```

#### 응답
```
{
  "_index" : "my_index",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 3,
  "result" : "deleted",
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 2,
  "_primary_term" : 1
}
```

인덱스 전체를 삭제할 수도 있다. 
</br>

#### 요청

```
DELETE my_index
```
<br>

### BULK

여러 명령을 배치로 수행하기 위해 bulkAPI 사용이 가능하며, index,create,update,delete의 동작이 가능하다.

```
$ curl -XPOST "http://localhost:9200/_bulk" -H 'Content-Type: application/json' --data-binary @bulk.json
```

bulk는 따로따로 수행하는 것보다 속도가 훨씬 빠르며, 대량의 데이터를 입력할때는 bulkAPI를 사용해야 불필요한 오버헤드가 없다.    
`Logstash`와 `Beats`는 데이터를 입력할때 `_bulk`를 사용하도록 개발되어 있다. 


참고링크:    
https://esbook.kimjmin.net/04-data/4.2-crud.  
https://esbook.kimjmin.net/04-data/4.3-_bulk
