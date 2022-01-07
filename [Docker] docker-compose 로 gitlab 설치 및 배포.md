# 1. 도커 컴포즈 파일 작성

```
version : "3"


services:
  gitlab:
    image: "gitlab/gitlab-ce:latest"
    container_name: gitlab
    restart: always
    hostname: "211.251.234.196:59090"
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://211.251.234.196:59090'
        nginx['enable'] = true
        nginx['listen_port'] = 80
        nginx['client_max_body_size'] = '10G'
        gitlab_rails['gitlab_shell_ssh_port'] = 6015
    ports:
      - "9090:80"
#     - "443:443"
      - "6015:22"
    volumes:
      - "./config:/etc/gitlab"
      - "./logs:/var/log/gitlab"
      - "./data:/var/opt/gitlab"
      - "./backup:/var/opt/gitlab/backups"
```

로컬에서 돌리는 것이 아니라 배포하는 깃랩이였기 때문에, nginx['listen_port'] = 80 를 통해 nginx로 외부 배포를 할 수 있게 해주는 것이 중요했다. 

이것 때문에 접속이 안되서 삽질 3시간했다ㅠㅠ


# 2. 포트 포워딩 및 방화벽 설정

위 문저의 ports 부분은 도커 내부와 리눅스 서버 포트를 매핑해주는 것이기 때문에 public 접근 포트를 설정해주었다.

또 기본적으로 방화벽으로 막혀 있기 때문에 해당 포트를 외부 접근 가능하도록 설정해주었다. 

# 3. gitlab root 비밀번호 설정

 ```
 docker exec -it [컨테이너 이름] /bin/bash //도커 접속
 gitlab-rails console -e production //rails 접속
 user = User.where(id: 1).first //root 유저 찾기
 user.password = '변경할 비밀번호'
 user.password_confirmation = '변경할 비밀번호'
 user.save //성공하면 true 리턴
 
 exit
 ```
 
 처음 접속하면 비밀번호 설정창이 나오기도 하는데 내 경우는 바로 로그인 화면이 떠서 gitlab-rails 를 통해 root 비밀번호를 설정해주고 접속했다.
 
 끝!
