# Docker파일 만들기

## Docker file 생성

```
# Start with a base image containing Java runtime
FROM openjdk:8-jdk-alpine

# Add Maintainer Info
LABEL maintainer="yurim2220@gmail.com"

# Add a volume pointing to /tmp
VOLUME /tmp

# Make port 5656 available to the world outside this container
EXPOSE 5656

ADD target/demo-backend-*.jar demo-backend.jar

# Run the jar file
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=dev","-jar","/demo-backend.jar"]

```
### 옵션

- **FROM** : 베이스 이미지로 해당 이미지를 실행시키는 기반 이미지. DockerFile은 `FROM`으로 시작. 이미지를 생성할때 FROM에 설정한 이미지가 로컬에 있으면 바로 사용하고, 없으면  Docker Hub에서 받아옴.
- **LABEL** : 라벨 정보 작성(docker inspect으로 label 확인 가능)
- **ENV** : 환경변수 설정
- **VOLUME** : 볼륨 마운트
- **EXPOSE** : export할 포트번호
- **ADD** : 파일/디렉터리 추가
- **ENTRYPOINT** : 컨테이너가 시작되었을때 스크립트 실행
- **RUN** : 도커 이미지가 생성되기 전에 수행할 쉘 명령어. FROM으로 설정한 이미지에 포한된 /bin/sh 실행파일을 사용하게 되면 /bin/sh 실행파일이 없으면 사용 불가
- **WORKDIR** : RUN,CMD,ENTRYPOINT 명령이 실행될 작업 디렉터리. 해당 디렉터리가 없다면 만들고, 사용자를 해당 디렉터리로 이동
- **COPY** : 호스트 OS의 로컬파일 또는 디렉토리를 이미지(컨테이너) 내의 경로로 복사 `COPY <Host파일의 복사할 파일 경로> <이미지에서 파일이 위치할 경로>`
- **CMD** : 컨테이너에서 실행할 명령을 지정. 즉 `docker run`명령으로 컨테이너를 생성하거나, `docker start` 명령으로 정지된 컨테이너를 시작할 때 실행되는 명령어를 지정. `CMD`는 도커 파일 내에서 한번만 사용 가능


cf) **RUN와 CMD 차이**    
`RUN`은 Build가 되는 시점에 실행되는 명령어 -> **이미지에 반영**  
`CMD`는 Container가 실행되는 시점에 실행되는 명령어 -> **컨테이너에 반영**


## Docker build 명령어로 컨테이너 이미지 빌드

```
docker build -t my-proj .
```
- **t** : tab의 약어. 이미지에 태그 지정
- **.** : 현재 디렉터리에서 Docker 파일을 찾으라 지정. 명령어 실행 위치에 따라 도커파일의 위치에 맞게 조정해주면 됨

## Docker run 으로 컨테이너 생성 및 실행
  
```
docker run -dp 5656:5656 my-proj 
```
- d :  컨테이너를 백그라운드에서 실행
- p : 호스트와 컨테이너 포트번호 매핑 (호스트: 컨테이너)


</br></br>
참고링크:   
https://code-masterjung.tistory.com/133   
https://velog.io/@monadk/Docker-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EB%A7%8C%EB%93%A4%EA%B8%B0   
