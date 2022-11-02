# Kubernetes

[쿠버네티스 실습 자료](https://github.com/shclub/newedu/blob/master/k8s_basic_hands_on.md) 를 보고 실습하면서 정리한 내용이다. 
</br>
쿠버네티스를 구성하는것은 많지만 그 중에서 Deployment, Service, Ingress/Route에 대해 중점적으로 살펴보자. 

</br></br>

## 리소스 개념

#### MasterNode, Workder Node

![image](https://user-images.githubusercontent.com/45115557/199512276-9cffe16b-eea4-428f-9058-d766d922b672.png)

클러스터 전체를 관리하는 컨트롤러로써 마스터노드가 존재하고, 컨테이너가 배포되는 머신인 워커노드가 존재한다. 


#### Node

![image](https://user-images.githubusercontent.com/45115557/199511290-8c863710-c7e2-42df-8081-1f5f2eb51827.png)

노드는 하나의 서버라고 보면 된다.    
kubelet은 쿠버네티스 마스터와 노드 간 통신을 책임지는 프로세스이며, 하나의 머신 상에서 동작하는 파드와 컨테이너를 관리한다. 
도커와 같은 컨테이너 런타임은 레지스트리에서 컨테이너 이미지를 가져와 묶여있는 것을 풀고 애플리케이션을 동작시킨다. 

</br>


#### 오브젝트

가장 기본적인 구성단위가 되는 기본 오브젝트(Basicc Object)와 기본 오브젝트를 생성하고 관리하는 추가적인 기능을 가진 컨트롤러(Controller)로 이루어진다. 오브젝트들은 오브젝트의 특성(설정정보)를 기술한 Object Spec으로 정의가 되고, 커맨트라인 혹은 yaml/sjon 파일로 스펙을 정의할 수 있다.    

</br>

#### Pod

![image](https://user-images.githubusercontent.com/45115557/199516434-0028676e-8b32-402c-965d-add0b4ab20c9.png)

Pod는 쿠버네티스의 가장 기본적인 배포 단위로, 하나 이상의 컨테이너를 포함한다.    
**pod 내 컨테이너는 ip 와 port를 공유하여 localhost를 통해 통신하고    
 pod 내 배포된 컨테이너 간 디스크 볼륨을 공유할 수 있다.**    
 예를들어 프론트 백엔드 두개의 컨테이너가 있다면 이를 묶어 하나의 pod로 배포할 수 있다. 로그 수집기와 애플리케이션을 reverse proxy등을 묶어 배포하는 것도 많이 쓰인다고 한다. 특히 로그 수집기는 다른 컨테이너로 배포할 경우, 일반적인 경우에는 컨테이너에 의해 파일 시스템이 분리되기 때문에 로그 수집기가 애플리케이션이 배포된 컨테이너의 로그파일을 읽는 것이 불가능 하지만, 쿠버네티스의 경우 하나의 pod 내에서는 컨테이너들끼리 볼륨을 롱유할 수 있어 다른 컨테이너의 파일을 읽어올 수 있다.   
 위 그림과 같이 애플리케이션에 사용하는 주변 프로그램을 같이 배포하는 패턴을 msa 아키텍처에서 Side Car Pattern 이라고 한다. (이 외에도 Ambassador, Adapter Container 패턴 등이 있다. )

</br>

#### Volume

![image](https://user-images.githubusercontent.com/45115557/199517829-efbb3c5d-4c20-4f0b-9d2f-3cca0e783bc9.png)


컨테이너 restart에 상관없이 파일을 영속적으로 저장해야 할때 사용하는 스토리지가 볼륨이다. pod가 기동할 때 컨테이너에서 마운트해서 사용한다. 
쿠버네티스는 다양한 외장 디스크를 추상화된 형태로 제공한다. iSCSI나 NFS 같은 온프레미스 기반의 일반적인 외장 스토리지 외에도, 클라우드의 외장 스토리지인 AWS EBS, Google PD 이외에도 github와 같은 오픈소스 기반의 스토리지 서비스를 지원한다. 

</br>

## Deployment, Service, Ingress 관계 Flow

![image](https://user-images.githubusercontent.com/45115557/199504975-3f3bbe5e-38f8-48db-8375-4a3ce1c79401.png)

* Deployment : 메타데이터 및 pos 리플리카 개수, 컨테이너 이미지, 이미지 포트 정의
* Service : 어노테이션 , 서비스 포트 / 타겟 포트
* Ingress/Route : 어노테이션, Ingress의 서비스 name,port 

</br></br>

## kubectl 명령어

```
# 화일 이름의 리소스를 적용한다. 없으면 insert 있으면 update
# 아래 create/update 명령어를 대체 할 수 있다. 
kubectl apply -f [화일이름]
# 화일 이름의 리소스를  생성한다.  
kubectl create -f [화일이름]
# 화일 이름의 리소스를  update  한다.
kubectl update -f [화일이름]
# 화일 이름의 리소스를  delete  한다.
kubectl delete -f [화일이름]

# 해당 리소스 정보를 보여준다
kubectl  get [리소스 타입] [리소스 이름]

# 해당 리소스 세부 정보를 보여준다
kubectl  describe [리소스 타입] [리소스 이름]

# 해당 리소스 로그 정보를 보여준다
kubectl  log  [리소스 이름]
# 해당 리소스를 삭제 한다
kubectl  delete  [리소스 타입] [리소스 이름]

# Pod ( Container ) 안에서 command를  할 수 있다.
kubectl exec -it  [PO 이름] /bin/sh
```




참고링크:   
https://github.com/subicura/workshop-k8s-basic/blob/master/guide/guide-03.md   
https://github.com/shclub/newedu/blob/master/k8s_basic_hands_on.md   
https://bcho.tistory.com/1256
