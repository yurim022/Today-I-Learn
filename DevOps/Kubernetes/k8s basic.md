# Kubernetes

[쿠버네티스 실습 자료](https://github.com/shclub/newedu/blob/master/k8s_basic_hands_on.md) 를 보고 실습하면서 정리한 내용이다. 
</br>
쿠버네티스를 구성하는것은 많지만 그 중에서 Deployment, Service, Ingress/Route에 대해 중점적으로 살펴보자. 

</br></br>

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
