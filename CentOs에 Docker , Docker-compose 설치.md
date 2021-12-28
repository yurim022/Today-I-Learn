
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



아래는 설치  
```
[root@watcher yum.repos.d]#  sudo yum install docker-ce docker-ce-cli containerd.io
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
Resolving Dependencies
--> Running transaction check
---> Package containerd.io.x86_64 0:1.4.12-3.1.el7 will be installed
---> Package docker-ce.x86_64 3:20.10.12-3.el7 will be installed
--> Processing Dependency: docker-ce-rootless-extras for package: 3:docker-ce-20.10.12-3.el7.x86_64
---> Package docker-ce-cli.x86_64 1:20.10.12-3.el7 will be installed
--> Processing Dependency: docker-scan-plugin(x86-64) for package: 1:docker-ce-cli-20.10.12-3.el7.x86_64
--> Running transaction check
---> Package docker-ce-rootless-extras.x86_64 0:20.10.12-3.el7 will be installed
---> Package docker-scan-plugin.x86_64 0:0.12.0-3.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

==============================================================================================================================================================================================================================================================
 Package                                                                Arch                                                Version                                                       Repository                                                     Size
==============================================================================================================================================================================================================================================================
Installing:
 containerd.io                                                          x86_64                                              1.4.12-3.1.el7                                                docker-ce-stable                                               28 M
 docker-ce                                                              x86_64                                              3:20.10.12-3.el7                                              docker-ce-stable                                               23 M
 docker-ce-cli                                                          x86_64                                              1:20.10.12-3.el7                                              docker-ce-stable                                               30 M
Installing for dependencies:
 docker-ce-rootless-extras                                              x86_64                                              20.10.12-3.el7                                                docker-ce-stable                                              8.0 M
 docker-scan-plugin                                                     x86_64                                              0.12.0-3.el7                                                  docker-ce-stable                                              3.7 M

Transaction Summary
==============================================================================================================================================================================================================================================================
Install  3 Packages (+2 Dependent packages)

Total download size: 93 M
Installed size: 381 M
Is this ok [y/d/N]: y
Downloading packages:
경고: /var/cache/yum/x86_64/7/docker-ce-stable/packages/docker-ce-20.10.12-3.el7.x86_64.rpm: Header V4 RSA/SHA512 Signature, key ID 621e9f35: NOKEY
Public key for docker-ce-20.10.12-3.el7.x86_64.rpm is not installed
(1/5): docker-ce-20.10.12-3.el7.x86_64.rpm                                                                                                                                                                                             |  23 MB  00:00:00     
(2/5): docker-ce-cli-20.10.12-3.el7.x86_64.rpm                                                                                                                                                                                         |  30 MB  00:00:00     
(3/5): containerd.io-1.4.12-3.1.el7.x86_64.rpm                                                                                                                                                                                         |  28 MB  00:00:00     
(4/5): docker-ce-rootless-extras-20.10.12-3.el7.x86_64.rpm                                                                                                                                                                             | 8.0 MB  00:00:00     
(5/5): docker-scan-plugin-0.12.0-3.el7.x86_64.rpm                                                                                                                                                                                      | 3.7 MB  00:00:00     
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                                                                                                                         137 MB/s |  93 MB  00:00:00     
Retrieving key from https://download.docker.com/linux/centos/gpg
Importing GPG key 0x621E9F35:
 Userid     : "Docker Release (CE rpm) <docker@docker.com>"
 Fingerprint: 060a 61c5 1b55 8a7f 742b 77aa c52f eb6b 621e 9f35
 From       : https://download.docker.com/linux/centos/gpg
Is this ok [y/N]: y
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : 1:docker-ce-cli-20.10.12-3.el7.x86_64                                                                                                                                                                                                      1/5 
  Installing : docker-scan-plugin-0.12.0-3.el7.x86_64                                                                                                                                                                                                     2/5 
  Installing : containerd.io-1.4.12-3.1.el7.x86_64                                                                                                                                                                                                        3/5 
  Installing : docker-ce-rootless-extras-20.10.12-3.el7.x86_64                                                                                                                                                                                            4/5 
  Installing : 3:docker-ce-20.10.12-3.el7.x86_64                                                                                                                                                                                                          5/5 
  Verifying  : docker-scan-plugin-0.12.0-3.el7.x86_64                                                                                                                                                                                                     1/5 
  Verifying  : docker-ce-rootless-extras-20.10.12-3.el7.x86_64                                                                                                                                                                                            2/5 
  Verifying  : containerd.io-1.4.12-3.1.el7.x86_64                                                                                                                                                                                                        3/5 
  Verifying  : 1:docker-ce-cli-20.10.12-3.el7.x86_64                                                                                                                                                                                                      4/5 
  Verifying  : 3:docker-ce-20.10.12-3.el7.x86_64                                                                                                                                                                                                          5/5 

Installed:
  containerd.io.x86_64 0:1.4.12-3.1.el7                                                docker-ce.x86_64 3:20.10.12-3.el7                                                docker-ce-cli.x86_64 1:20.10.12-3.el7                                               

Dependency Installed:
  docker-ce-rootless-extras.x86_64 0:20.10.12-3.el7                                                                                  docker-scan-plugin.x86_64 0:0.12.0-3.el7                                                                                 

Complete!
```
