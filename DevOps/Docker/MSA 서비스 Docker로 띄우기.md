### 네트워크 생성

Docker에서 container간 network를 통해 통신할 수 있다. 
먼저 docker에서 기본으로 제공되는 network에는 Bridge, Host, None 이 있다. 

![image](https://user-images.githubusercontent.com/45115557/202164803-d3dce4d6-5f5b-4349-8d54-cbd5a76647d8.png)

#### Docker network driver bridge

Docker를 설치하게 되면 자동으로 Host machine의 Network interface에 Docker0 이라는 Virtual interface가 생성된다.    
Docker0은 host machine의 네트워크에 생성이되며 Container가 생성될때 사용자가 특정 network driver를 설정한 것이 아니라면 default로 Docker0이라는 Network interface에 연결이 된다. 
각 Container의 veth 인터페이스와 바인딩되며 Host Os의 eth0 인터페이스와 이어주는 역할을 한다. Docker0은 일반적인 인터페이스가 아니고 virtual ethernet bridge이다. 

<img width="431" alt="image" src="https://user-images.githubusercontent.com/45115557/202166381-57e7a278-9e8e-4d1e-b306-24b9d3764c83.png">

Docker0의 Gateway는 자동으로 172.17.0.1로 설정되며 16 bit netmask(255.255.0.0)로 설정된다. 이 ip는 DHCP를 통해 할당받는 것이 아니고 docker 내부 로직에 의해 자동 할당 되는 것이다. 















 
참고링크:   
https://jangseongwoo.github.io/docker/docker_container_network/   
