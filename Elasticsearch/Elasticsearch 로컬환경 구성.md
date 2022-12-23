# Elasticsearch 로컬환경 구성

elasticsearch와 kibana를 묶어서 docker-compose로 설치하려 한다.    
먼저 elastisearch를 구동하는데 discovery.seed_hosts, cluster.initial_master_nodes 값 설정이 필요하다는 에러가 떴다. 
default가 multi node이기 때문에 나는 에러이다. 나는 일단 로컬 테스트용이여서 아래를 추가해주었다.    

```
"discovery.type=single-node"
```

이때  cluster.initial_master_nodes랑 discovery.seed_hosts는 주석처리해주어야 한다. 안그러면 멀티설정이 우선권한을 가져버린다. 


kibana를 올리는데 이런 에러가 났다...
```
...APIs are disabled because the Encrypted Saved Objects plugin is missing encryption key. Please set xpack.encryptedSavedObjects.encryptionKey in the kibana.yml or use the bin/kibana-encryption-keys command."}
["error","elasticsearch-service"],"pid":7,"message":"Unable to retrieve version information from Elasticsearch nodes. connect ECONNREFUSED 127.0.0.1:9200"}
```

xpack 설정에 문제가 있는가보다.




</br>
   
참고링크:   
https://juntcom.tistory.com/121   
https://discuss.elastic.co/t/set-password-and-user-with-docker-compose/225075   
