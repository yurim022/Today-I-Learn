## 1. Docker 이미지 다운로드
```
docker search mysql
```
 도커 이미지를 검색해보면 가장 상단에 mysql 이미지가 나온다 .
 
 ```
 docker pull mysql
 ```
 
 
 ## 2. 컨테이너 생성 및 실행
 
 ### 명령어를 통해 실행
 ```
 docker run -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=password --name my_mysql
 ```
 - -p 3306:3306 호스트의 포트와 컨테이너의 포드를 연결
 - -e MYSQL_ROOT_PASSWORD=password 컨테이너를 생성하면서 환경변수 지정
 - --name 컨테이너의 이름 설정
 
 ### Docker compose 파일 생성
 
 명령어를 통해 하는 방법도 있지만, 개인적으로 컴포즈 파일을 만들어서 하는 것을 좋아한다. 
```

```




![image](https://user-images.githubusercontent.com/45115557/168542187-bae9988f-a3b3-4a3e-ad12-22c4af66dcf1.png)
