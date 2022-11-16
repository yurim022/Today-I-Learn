## 네트워크 생성

Docker에서 container간 network를 통해 통신할 수 있다. 
먼저 docker에서 기본으로 제공되는 network에는 Bridge, Host, None 이 있다. 

* **Bridge network** : 호스트와 별도의 가상의 네트워크를 만들고 컨테이너를 배치해서 사용하는 방식
* **Host network** : 호스트의 네트워크 환경을 게스트 네트워크에서 그대로 사용. 포트 포워딩 없이 내부 어플리케이션 사용
* **Non network** : 네트워크를 사용하지 않음. lo네트워크만 사용, 외부와 단절

![image](https://user-images.githubusercontent.com/45115557/202164803-d3dce4d6-5f5b-4349-8d54-cbd5a76647d8.png)

</br>

### Docker network driver bridge

Docker를 설치하게 되면 자동으로 Host machine의 Network interface에 Docker0 이라는 Virtual interface가 생성된다.    
Docker0은 host machine의 네트워크에 생성이되며 Container가 생성될때 사용자가 특정 network driver를 설정한 것이 아니라면 default로 Docker0이라는 Network interface에 연결이 된다. 
각 Container의 veth 인터페이스와 바인딩되며 Host Os의 eth0 인터페이스와 이어주는 역할을 한다. Docker0은 일반적인 인터페이스가 아니고 virtual ethernet bridge이다. 

<img width="431" alt="image" src="https://user-images.githubusercontent.com/45115557/202166381-57e7a278-9e8e-4d1e-b306-24b9d3764c83.png">

Docker0의 Gateway는 자동으로 172.17.0.1로 설정되며 16 bit netmask(255.255.0.0)로 설정된다. 이 ip는 DHCP를 통해 할당받는 것이 아니고 docker 내부 로직에 의해 자동 할당 되는 것이다. 

</br>

### 사용자 정의 네트워크 생성

```
docker network create --gateway 172.18.0.1 --subnet 172.18.0.0/16 ecommerce-network
```

gateway와 subnet을 지정하지 않고 만들수도 있지만, 이때 ip를 지정해서 컨테이너를 띄우고 싶은 경우 오류가 날 수 있다. 

</br>

### 네트워크 정보 확인

```
docker inspect ecommerce-network
```

inspect 명령어를 통해 네트워커의 gateway,subnet, 연결된 container 등의 정보를 확인할 수 있다. 

![image](https://user-images.githubusercontent.com/45115557/202169843-bc689d89-6f86-40c1-b65b-06c0b1f33950.png)

일반적인 컨테이너는 하나의 guest os라고 생각하면 된다. 각각의 guest os마다 고유의 ip가 할당이 되게 되는데,    
**같은 network에 포함된 컨테이너는 ip address 외에도 container id, container 이름을 통해서 통신할 수 있다.** ip address는 변경될 수 있기 때문에 컨테이너 이름을 통해 네트워크 해주어야 한다. 

</br>







 
참고링크:   
https://jangseongwoo.github.io/docker/docker_container_network/   
