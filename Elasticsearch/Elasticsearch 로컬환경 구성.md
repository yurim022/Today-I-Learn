# Elasticsearch 로컬환경 구성

elasticsearch와 kibana를 묶어서 docker-compose로 설치하려 한다.    
먼저 elastisearch를 구동하는데 discovery.seed_hosts, cluster.initial_master_nodes 값 설정이 필요하다는 에러가 떴다. 
default가 multi node이기 때문에 나는 에러이다. 나는 일단 로컬 테스트용이여서 아래를 추가해주었다.    

```
"discovery.type=single-node"
```

이때  cluster.initial_master_nodes랑 discovery.seed_hosts는 주석처리해주어야 한다. 안그러면 멀티설정이 우선권한을 가져버린다. 

</br>

kibana를 올리는데 이런 에러가 났다...
```
...APIs are disabled because the Encrypted Saved Objects plugin is missing encryption key. Please set xpack.encryptedSavedObjects.encryptionKey in the kibana.yml or use the bin/kibana-encryption-keys command."}
["error","elasticsearch-service"],"pid":7,"message":"Unable to retrieve version information from Elasticsearch nodes. connect ECONNREFUSED 127.0.0.1:9200"}
```
</br>

xpack 설정에 문제가 있는가 해서 disable 시켜줬는데,,,그래도 안된다ㅠ

```
xpack.ml.enabled: false
xpack.monitoring.collection.enabled: false
xpack.security.enabled: false
xpack.watcher.enabled: false
ingest.geoip.downloader.enabled: false
```

</br>
  
찾아보니 ssl 문제인것 같은데,,, 127.0.0.1:9200 으로 접근해서 문제가 있는 듯 했다. 

```
      ELASTICSEARCH_URL: http://es01:9200
      ELASTICSEARCH_HOSTS: http://es01:9200
```

위와같이 `containername:9200` 으로 접근하니 성공적으로 접속할 수 있었다!

</br>

![image](https://user-images.githubusercontent.com/45115557/213419553-7ddcb25b-b2e0-4fe4-b58b-f28f0d334ae0.png)

성공~!!!


### elk docker-compse.yml

```
version: '3.8'
services:
  es01:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.17.7
    container_name: es01
    environment:
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - ELASTIC_USERNAME=elastic
      - ELASTIC_PASSWORD=elastic
      - "discovery.type=single-node"
      - "node.data:true"
      - "network.host:0.0.0.0"
#      - "discovery.seed_hosts:[]"
#      - "cluster.initial_master_nodes:[]"
      - http.cors.enabled=true
      - http.cors.allow-origin=*
      - bootstrap.memory_lock=true
      - xpack.ml.enabled=false
      - xpack.monitoring.collection.enabled=false
      - xpack.security.enabled=false
      - xpack.watcher.enabled=false
      - ingest.geoip.downloader.enabled=false
    ports:
      - 9200:9200
      - 9300:9300
    networks:
      - elk

  kibana:
    image: docker.elastic.co/kibana/kibana:7.17.7
    container_name: kibana
    ports:
      - 5601:5601
    environment:
      ELASTICSEARCH_URL: http://es01:9200
      ELASTICSEARCH_HOSTS: http://es01:9200
      XPACK_MONITORING_ENABLED: "false"
      XPACK_MONITORING_COLLECTION_ENABLED: "false"
      XPACK_SECURITY_ENABLED: "false"
      ELASTICSEARCH_USERNAME: "elastic"
      ELASTICSEARCH_PASSWORD: "elastic"
    depends_on:
      - es01
    networks:
      - elk

networks:
  elk:
    driver: bridge
```


최종 docker-compose 파일. cors-allow all 같은건 로컬인 경우에만 사용하고 상용에서는 상세한 설정을 해서 사용해야 한다. 


</br>
   
참고링크:   
https://juntcom.tistory.com/121   
https://discuss.elastic.co/t/set-password-and-user-with-docker-compose/225075      
https://koocci-dev.tistory.com/18.  

 
