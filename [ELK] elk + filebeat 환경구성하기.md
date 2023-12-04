# ELK + filebeat 환경 구성하기

우선 elk는 docker를 이용하여 구성하였다.    
[docker-elk 깃허브 링크](https://github.com/deviantony/docker-elk) 를 참고하여 띄웠다.    

logstash의 pipeline 폴더 내의 logstash.conf 파일을 다음과 같이 수정해주었다. 

### logstash.conf
```
input {
       # FileBeat를 통해 로그 수신
       beats {
               port => 5000
               host => "0.0.0.0"
               ssl => false
       }

}

filter {
       # Grok형식으로 들어오는 로그를 가공하기 위한 필터
        grok {
                # 로그 안에 LOGLEVEL 패턴이 있을 경우 파싱하여 log_level이라는 필드로 추가
                # [INFO ]와 같이 스페이스를 남기는 설정을 고려하여 파싱함
                match => [
                    "message", "\[%{LOGLEVEL:log_level}%{SPACE}*\]"
                ]
        }
}


output {
        elasticsearch {
                hosts => "elasticsearch:9200"
                user => "elastic"
                password => "changeme"
                ecs_compatibility => disabled
                index => "logstash-%{+YYYY.MM.dd}"
                data_stream => false
        }
}
```

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



