# Docker 이미지 만들기

![image](https://user-images.githubusercontent.com/45115557/194799943-e2d479f4-7a93-4271-9a9c-9ecee9c3c6da.png)

도커이미지를 생성하는 방법은 build와 commit 두가지가 있다. 
build는 도커 파일을 통해 만들고 싶은 이미지를 생성하는 것이고, commit은 이미 사용하고 있는 컨테이너를 이미지로 백업하는 것이다.    
commit은 어떤 과정을 통해서 이미지가 생성되었는지 알기 힘들기 때문에 dockerfile로 남기는것이 나에게도, 남들에게도 더 좋지 않나 싶다^.^

</br></br>
## commit

![image](https://user-images.githubusercontent.com/45115557/194800116-42cb8fb6-482e-4f9b-940f-59a34d5dfe63.png)

수정한 컨테이너에 commit을 하면 그 컨테이너가 새로운 이미지가 된다. 새로운 이미지를 push하면 다른 개발자들도 pull 을 받을 수 있고, docker hub와 같은 레지스트리에 업로드하면 전 세계 사람들이 pull 받을 수 있다. 
</br></br>

<img width="1300" alt="image" src="https://user-images.githubusercontent.com/45115557/194803324-4ee44b92-8919-4f9b-8221-4a313605185c.png">

다음과 같이 ubuntu 파일을 다운받아서 docker를 실행시킨 후 git을 설치하고 이를 이미지로 빌드한다고 했을 때, 

```
docker run -it --name my-ubuntu ubuntu bash
```
it 옵션을 줘서 컨테이너를 실행하자 마자 컨테이너 쉘로 들어간 후 

```
apt update && apt install git
```
컨테이너에서 git을 실행시켜준다.

```
docker commit my-ubuntu yurimming:ubuntu-git
```
을 해주면 ubuntu에 git이 깔린 이미지 파일이 생성되게 된다. 
이때 commit 뒤에 [이미지화할 컨테이너 이름][레포지토리:이미지 태그]
순으로 작성해주면 된다.

자세한건 생활코딩님의 [도커 이미지 빌드 commit](https://www.youtube.com/watch?v=RMNOQXs-f68) 영상을 참고하자.

</br></br>
## Docker file

```dockerfile
FROM adoptopenjdk/openjdk11
CMD ["./mvnw", "clean", "package"]
ARG JAR_FILE_PATH=target/*.jar
COPY ${JAR_FILE_PATH} app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]

```

### DockerFile 옵션

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

</br></br>
### Docker build 명령어로 컨테이너 이미지 빌드

```
docker build -t my-proj .
```
- **t** : tab의 약어. 이미지에 태그 지정
- **.** : 현재 디렉터리에서 Docker 파일을 찾으라 지정. 명령어 실행 위치에 따라 도커파일의 위치에 맞게 조정해주면 됨

</br></br>
### Docker run 으로 컨테이너 생성 및 실행
  
```
docker run -dp 5656:5656 my-proj 
```
- d :  컨테이너를 백그라운드에서 실행
- p : 호스트와 컨테이너 포트번호 매핑 (호스트: 컨테이너)


</br></br>
참고링크:   
https://www.youtube.com/watch?v=0kQC19w0gTI   
https://velog.io/@miracle-21/DOCKER%EC%9D%B4%EB%AF%B8%EC%A7%80-%EB%A7%8C%EB%93%A4%EA%B8%B0-Build   
https://code-masterjung.tistory.com/133   
https://velog.io/@monadk/Docker-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EB%A7%8C%EB%93%A4%EA%B8%B0   
