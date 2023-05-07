# ElasticSearch 

## 환경구성

Elasticsearch는 Java 가상머신 위에서 실행이 되는데, 7.0 기준 1gb의 힙메모리가 기본으로 설정되어있다.   
이 설정들은 jvm.options 파일에서 아래 내용들을 수정하여 변경할 수 있다. 

### config/jvm.options

```
-Xms1g
-Xmx1g
```

### elasticsearch.yml

```
cluster.name: "<클러스터명>"
```

Elasticsearch의 노드들은 클러스터명이 같으면 같은 클러스터로 묶이고, 클러스터명이 다르면 동일한 물리적 장비나 바인딩이 가능한 네트워크상에 있더라도 서로 다른 클러스터로 바인딩 된다.   
default 클러스터면은 "elasticsearch" 이며 충돌을 방지하기 위해 클러스터명은 반드시 고유한 이름으로 설정하도록!

<br>

```
node.name: "<노드명>"
```

실행중인 각각의 elasticsearch 노드들을 구분할 수 있는 노드의 이름을 설정 할 수 있음.

<br>

```
discovery.seed_hosts: ["<호스트1>","<호스트2>", ...]
```

클러스터 구성을 위해 바인딩 할 원격 노드의 IP 또는 도메인 주소를 배열 형태로 입력한다.   
주소만 적은 경우 디폴트로 9300~9305 사이의 포트값을 검색하며, tcp 포트가 이 범위 밖에 설정된 경우 포트번호까지 같이 적어줘야 한다!   
원격에 있는 노드를 찾아 바인딩 하는 과정을 디스커버리 라고 한다. 

<br>

```
cluster.initial_master_nodes: [ "<노드-1>", "<노드-2>" ]
```

클러스터가 최초 실행될때 명시된 노드를 대상으로 마스터 노드를 선출한다. 

<br>

## 노드의 역할

elasticsearch의 노드는 수행하는 다양한 역할들이 있는데, 각자의 노드들이 서로 다른 역할들을 수행하도록 클러스터를 구성할 수 있다. 

```
node.master:true
```

마스터 후보 노드 여부 설정. false인 경우 이 노드는 마스터 노드로 선출이 불가하다.    
모든 클러스터는 1개의 마스터 노드가 존재하며, 마스터 노드가 다운되거나 끊어진 경우 남은 마스터 후보 노드들 중에서 새로운 마스터 노드가 선출되게 된다.    

<br>

```
node.data: true
```

노드가 데이터를 저장하도록 한다. 

<br>

```
node.ingest: true
```

데이터 색인시 전처리 작업인 ingest pipeline 작업의 수행을 할 수 있는지 여부를 지정한다. false인 경우 이 노드에서는 ingest pipeline 작업의 실행이 불가능!

```
node.ml : true
```

머신러닝 작업을 수행할 수 있는지 여부

<br>

### 예시

```
node.master: true
node.data: false
node.ingest: false
node.ml: false
```

다음과 같이 데이터를 저장하거나 색인하지 않고, 오직 클러스터 상태를 관리하는 마스터 노드의 역할만 수행하도록 설정할 수 있다.    
모든 설정값을 false로 한다면 저장, 색인, 클러스터 상태 업데이트도 하지 않을 채 클라이언트와 통신하는 역할로만 사용할 수 있다. 이러한 노드를 coordinate only node라 한다. 


<br>

출처:   
https://esbook.kimjmin.net/02-install/2.3-elasticsearch/2.3.2-elasticsearch.yml   
https://esbook.kimjmin.net/02-install/2.3-elasticsearch/2.3.3-node-settings   
