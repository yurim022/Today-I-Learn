# Docker파일 만들기

## Docker file 생성

- FROM : 베이스 이미지로 해당 이미지를 실행시키는 기반 이미지
- LABEL : 
- VOLUME : 
- EXPOSE : 
- ADD : 
- ENTRYPOINT : 
- RUN : 도커 이미지가 생성되기 전에 수행할 쉘 명령어 
- COPY : Docker 클라이언트의 현재 디렉토리에서 파일을 추가 (COPY (source) (dist))
- CMD : 컨테이너에서 실행할 명령을 지정, 도커 파일 내에서 한번만 사용 가능


## Docker build 명령어로 컨테이너 이미지 빌드

```
docker build -t my-proj .
```
- t: 이미지에 태그 지정. 해당 이미지 이름
- . 현재 디렉터리에서 Docker 파일을 찾으라 지정. 명령어 실행 위치에 따라 도커파일의 위치에 맞게 조정해주면 됨

## Docker run 으로 컨테이너 생성 및 실행
  
```
docker run -dp 5656:5656 my-proj 
```
- d :  컨테이너를 백그라운드에서 실행
- p : 호스트와 컨테이너 포트번호 매핑 (호스트: 컨테이너)

참고링크:   
https://code-masterjung.tistory.com/133
