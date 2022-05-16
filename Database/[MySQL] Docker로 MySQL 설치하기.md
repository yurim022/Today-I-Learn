## 1. Docker 이미지 다운로드
```
docker search mysql
```
 도커 이미지를 검색해보면 가장 상단에 mysql 이미지가 나온다 .
 ![image](https://user-images.githubusercontent.com/45115557/168542477-66d875b3-3664-4cc5-9a87-97648fb020b5.png)

 
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
 설정값들을 관리하기 쉽기 때문!
```
vi docker-compose-mysql.yml
```

```
version: "3"
services:
  db:
    image: mysql
    container_name: yurim_mysql
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: "yurim022"
    command:
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci
    volumes:
      - /home/yurimcho/develop/mount:/var/lib/mysql #host:container

```
컴포즈 파일에 설정값들을 등록해준 뒤 컨테이너 생성 및 실행을 해준다. 
- -f 뒤에 생성/올리고 싶은 컴포즈 파일
- -d 컨테이너가 백그라운드에서 돌도록 설정

```
docker-compose -f docker-compose-mysql.yml up -d
```

### 컨테이너 생성 확인

```
docker ps 
```
![image](https://user-images.githubusercontent.com/45115557/168543152-0b17f062-e17f-4134-b2e2-0e315e835a7a.png)

컨테이너가 잘 돌아가고 잇는 것을 볼 수 있다. 


### MySQL 접속

```
docker exec -it yurim_mysql bash
```
명령어로 mysql 컨테이너 내에 접속한 후, 

```
mysql -u root -p
```
이후에 앞서 컴포즈 파일에서 설정한 비밀번호를 치고 들어가면 된다.
데이터베이스가 생성된 뒤에는 -p 뒤에 데이터베이스 이름을 적으면 된다.


![image](https://user-images.githubusercontent.com/45115557/168542187-bae9988f-a3b3-4a3e-ad12-22c4af66dcf1.png)
