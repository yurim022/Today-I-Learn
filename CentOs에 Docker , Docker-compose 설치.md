
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
Error: Package: docker-ce-rootless-extras-20.10.12-3.el7.x86_64 (docker-ce-stable)
           Requires: slirp4netns >= 0.4
Error: Package: docker-ce-rootless-extras-20.10.12-3.el7.x86_64 (docker-ce-stable)
           Requires: fuse-overlayfs >= 0.7
           
다음과 같은 에러를 만날 수 있다. 
