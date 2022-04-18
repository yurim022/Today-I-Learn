## pgadmin 으로 db 데이터 backup

dev환경에 있던 데이터를 test db로 옮겨야 하는 요구사항이 있었다. 
사실 db backup 같은 경우, 특정한 간격으로 스크린샷을 찍어놓고 변화된 부분만 따로 모아놨다가 한꺼번에 변경시키거나, 변화된 데이터가 너무 많을경우 스크린샷으로 아예 동기화 시켜버리는 식으로 진행되기 때문에
직접 데이터베이스에 터미널로 접속해서 데이터를 넣는 경우는 많지 않다. 하지만 devops를 하다보면 간간히 하게 되는 작업....^,6

pgadmin의 데이터를 insert 문의 형태로 추출해 보자.
해당 데이터는 임의의 테이블을 만들어 저장 시퀀스만 참고할 수 있도록 만들었다. 

## 1. 데이터 추출

<img width="699" alt="스크린샷 2022-04-18 오전 10 55 32" src="https://user-images.githubusercontent.com/45115557/163847632-2ed3147e-4577-4305-b904-947d53580c9b.png">
파일 이름을 지어주고, 포멧은 plain으로 해준다. encoding은 UTF8


<img width="699" alt="스크린샷 2022-04-18 오전 10 55 32" src="https://user-images.githubusercontent.com/45115557/163847656-21d2dc19-420e-4f3d-81cd-8d65b1df4456.png">
다음 탭에서 데이터를 추출할 것이기 때문에 data를 선택해준다. 

![image](https://user-images.githubusercontent.com/45115557/163847672-326db2e9-d882-439a-92eb-c0be11c6b1e8.png)
options 탭에서 어떤 형태로 추출할 것인지 선택할 수 있는데, insert문을 옵션으로 선택해준다. 






## 2. 도커 내부의 파일을 옮겨준다.

로컬에 설치했다면 상관없지만, 내 경우 도커에서 pgadmin을 띄웠기 때문에 docker에서 파일을 밖으로 빼주어야 했다. 
![image](https://user-images.githubusercontent.com/45115557/163848293-77aeb2ab-b397-4bff-ad85-9f8a3e17d79e.png)
백업 버튼을 누른 후 실행된 명령어를 보면 파일경로를 알 수 있다. 

### 컨테이너 -> 호스트
```
docker cp [container name]:[container 내부경로] [host 파일경로]
```


해당 구문에 맞게 실행해주면 다음과 같이 호스트에 파일이 잘 복사된것을 볼 수 있다. 
![image](https://user-images.githubusercontent.com/45115557/163848940-08b8ff6d-037f-44c9-a8f8-58952e56964f.png)







## 3. db 환경에서 구문 실행
```
psql -f [backup_filename]
```

해당 명령어를 실행하면 파일 내 insert문들이 실행되면서 데이터가 들어가게 된다. 

끗^___________________________^
