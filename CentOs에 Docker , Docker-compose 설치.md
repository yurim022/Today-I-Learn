
# Docker 설치

### 1. docker repository 설정
```
// yum-utils 설치
# yum install -y yum-utils

// yum-config-manager 를 통해서 docker 저장소 설정
# yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

### 2. docker engine 설치
```
# yum install docker-ce docker-ce-cli containerd.io
```

이 때, 
```
Error: Package: docker-ce-rootless-extras-20.10.12-3.el7.x86_64 (docker-ce-stable)
           Requires: slirp4netns >= 0.4
           
Error: Package: docker-ce-rootless-extras-20.10.12-3.el7.x86_64 (docker-ce-stable)
           Requires: fuse-overlayfs >= 0.7
```        
다음과 같은 에러를 만날 수 있다. 

이때 /etc/yum.repos.d/docker-ce.repo 경로로 가서 해당 위치에 추가해준다.
```
[centos-extras]
name=Centos extras - $basearch
baseurl=http://mirror.centos.org/centos/7/extras/x86_64
enabled=1
gpgcheck=1
gpgkey=http://centos.org/keys/RPM-GPG-KEY-CentOS-7
```

그 후, 아래 명령어를 실행하여 위 에러에서 필요하다고 했던 패키지들을 설치해준다. 
```
yum -y install slirp4netns fuse-overlayfs container-selinux
```

그리고 다시 
```
# yum install docker-ce docker-ce-cli containerd.io
```
를 해주면 최신 버전의 도커가 설치된다!
![image](https://user-images.githubusercontent.com/45115557/147519591-5e77a305-ae12-4336-921a-efd1e6292a09.png)

