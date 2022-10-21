## m2파일 분리 배경

현재 프로젝트는 회사에서 자체적으로 운영하는 maven repository주소를 바라보도록 되어있다. 
해서 자동으로 회사 리포지토리에서 패키지들을 받아오게 되는데, springboot 교육을 받으면서 해당 환경을 분리해야 하는 요구사항이 있었다. 

## intellij m2폴더 경로 변경 

<img width="910" alt="image" src="https://user-images.githubusercontent.com/45115557/175503851-e4ec35be-e634-4ac2-ad44-99fc5d66a8a4.png">
intellij 의 maven 설정창에 들어가봐면 현재 내 m2 폴더 경로가 나와있다.
폴더를 work, study 등과 같이 나눠주고, 그 및에 m2 폴더를 만들어준 뒤 intellij 실행시 용도에 받게 변경해준다. 


## settings.xml 파일 수정 및 respository 분리
<img width="301" alt="image" src="https://user-images.githubusercontent.com/45115557/175504237-8d56f3b0-426b-48da-bde4-1f4dd0fc0e72.png">
m2 폴더 및에 repository가 로컬리포지터리이고, settings.xml에 있는 설정으로 원격저장소에 접근한다. 
필요에 따라 settings.xml을 수정해주면 된다!!!


#### settings.xml
```
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                          https://maven.apache.org/xsd/settings-1.0.0.xsd">
      <servers>

  // 여기 필요에 맞게 수정

      </servers>

      <mirrors>
 // 여기 필요에 맞게 수정
      </mirrors>

</settings>

```
