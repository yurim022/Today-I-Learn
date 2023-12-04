# ELK 환경 구성하기

우선 elk는 docker를 이용하여 구성하였다.    
[docker-elk 깃허브 링크](https://github.com/deviantony/docker-elk) 를 참고하여 띄웠다. 

</br>

## filebeat 구성

로컬환경에서 구성하였기 때문에, filebeat는 mac의 brew를 통해서 설치했다.

```
brew install filebeat
```

우선 install 명령어를 통해 `filebeat`를 설치하면 `/usr/local/etc/filebeat/filebeat.yml` 해당 경로에 yml 파일이 생성된다.    
이를 커스텀 하고 싶으면 파일 삭제후 다시 만들거나, 혹은 vi 명령어로 수정해서 사용하면 된다. 

</br>

### filebeat.yml

```
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /logs/studioapi/{log-file-path}/{log-file}.log

  fields:
    env: local  # 추가해주고 싶은 필드

setup.kibana:
  host: "localhost:5601"

output.logstash:
  hosts: ["localhost:5000"]
```

각자 환경에 맞에 filebeat.yml 파일을 수정해준다. 



