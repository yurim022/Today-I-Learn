# ELK + filebeat 환경 구성하기

## ELK 구성

![image](https://github.com/yurim022/Today-I-Learn/assets/45115557/d978c25f-030f-44af-be88-3334fea431d0)


우선 elk는 docker를 이용하여 구성하였다.    
`filebeat 로 어플리케이션의 로그 수집 -> logstash에서 수신 및 필터 -> elastic 저장 -> kibanana 모니터링` 의 간단한 형태로 구성하였다.    
대용량 로그처리의 경우 중간에 kafka를 사용하여 유실을 방지하는 케이스도 있으나, 이 경우 로그양이 많지 않아 간단하게 진행하였다.
> Logstash와 Filebeat는 sharding/replication을 지원하지 않기 때문에 ELK 스택이 다운됬을때 로그가 전달되지 않는다. 

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

### 포트정보

* elasticsearch : 9200
* kibana : 5601
* logstash : 5000

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

</br>

설정파일 수정이 완료되면 start 명령어로 filebeat를 실행시킨다.     


```
brew services start filebeat
```

</br>
   
아래 명령어를 치고 로그가 잘 나오면 제대로 띄워진 것이다.    

```
 sudo /usr/local/Cellar/filebeat/{target-version}/bin/filebeat -e
```

</br>
   
에러가 발생한다면, 해당하는 권한을 설정해주면 된다.   

```
Exiting: error loading config file: config file ("/usr/local/etc/filebeat/filebeat.yml") must be owned by the user identifier (uid=0) or root

> sudo chown root /usr/local/etc/filebeat/filebeat.yml
> sudo chgrp wheel /usr/local/etc/filebeat/filebeat.yml
```

</br></br>


## Kibana 로그 모니터링
    

키바나 서버 (default 5601 port) 에 접속 > `Stack Management >  Create Index Pattern` 선택     

![image](https://github.com/yurim022/Today-I-Learn/assets/45115557/954f50f7-e277-4aef-8d49-6a42c5a0ada8)

</br>

`logstash.conf` 파일에서 설정한 인덱스 패턴을 입력해준다. (필자의 경우 index => "logstash-%{+YYYY.MM.dd}")   

![image](https://github.com/yurim022/Today-I-Learn/assets/45115557/703682ff-310d-4ca5-8777-0906ea9c23ec)

</br>

`Discover` 탭으로 이동해서 인덱스에 해당하는 로그를 볼 수 있다.  

![image](https://github.com/yurim022/Today-I-Learn/assets/45115557/4083e4fb-e06a-4cac-ab1b-80fa8517020e)

</br>

오른쪽 상단의 필터로 timestamp 조절도 할수 있다.   
![image](https://github.com/yurim022/Today-I-Learn/assets/45115557/3b8640bf-a6be-4eb9-b011-cc25b06ca0c2)

</br>

KSQL 검색도 가능하다.   
![image](https://github.com/yurim022/Today-I-Learn/assets/45115557/b50cc671-6ef7-4032-8bdd-41d6c0f43cf2)

</br>

검색결과 화면
![image](https://github.com/yurim022/Today-I-Learn/assets/45115557/12fe7381-ae28-41af-b091-ff774bd5b30d)



</br>

</br></br>

참고링크:   
https://kanoos-stu.tistory.com/21#:~:text=Filebeat%20%EC%84%A4%EC%B9%98%20brew%20install%20filebeat%20mac%EC%97%90%EC%84%9C%EB%8A%94%20brew%20%EB%AA%85%EB%A0%B9%EC%96%B4%EB%A5%BC,%ED%8F%B4%EB%8D%94%20%EB%82%B4%EB%B6%80%EC%97%90%20%EC%84%A4%EC%A0%95%20%ED%8C%8C%EC%9D%BC%EC%9D%B8%20filebeat.yml%20%ED%8C%8C%EC%9D%BC%EC%9D%B4%20%EC%9C%84%EC%B9%98%ED%95%B4%20%EC%9E%88%EB%8B%A4.   
https://mangkyu.tistory.com/197   
https://yonikim.tistory.com/22   
https://hello-startup.tistory.com/5    
https://kanoos-stu.tistory.com/77   
