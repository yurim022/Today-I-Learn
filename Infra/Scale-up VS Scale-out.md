# Scale up VS Scale out


시스템에 트래픽이 증가하여 서비스가 감당하지 못할 수준이 되었다고 가정하자. 이때 우리는 어떻게 문제를 해결할 수 있을까?   
**인프라 확장**으로 문제를 해결한다면 스케일 업, 스케일 아웃 둘 중에 무엇을 할지 정해야 할 것이다. 그렇다면 각각의 장단점은 무엇이고 상황에 따라 어떤 적용이 적합한지 판단하는 근거에 대해 . 

![image](https://user-images.githubusercontent.com/45115557/184940767-9d759fd8-4f37-4968-8133-e797cd0a95ac.png)


## Scale up

* 물리적인 성능 향상(CPU, MEMORY,,)
* 수직 스케일링(vertical scaling)
* 단순하거나 빠른 작업(DB 갱신이 자주 일어나야 하는 경우)에 적합. scale out에서는 데이터 정합성을 유지하기 어렵기 때문
* 고비용, 고효율


### 장점
* 추가적인 네트워크 연결 없이 용량을 증강할 수 있다.
* 추가되는 용량이나 업그레이드 비용만 부가되기에 비용적인 증강이 스케일 아웃에 비해 낮고, 설계가 쉽다. 
* **데이터 정합성 이슈에서 자유롭다.**

### 단점
* 스케일업을 할수록 기존 하드웨어의 냉각, 공간 전력공급 이슈가 있을 수 있다.
* 하드웨어의 한계로 특정범위 이상 업그레이드를 할 수 없다. 
* 스케일업의 일정 수준을 넘어가면 성능 증가 폭이 미미해진다. ex) cpu i3 -> i5 (1.5배) / cpi i5 -> i7 (1.2배)
* **서버 한대에 모든 부하가 집중되므로, 장애 발생시 서비스를 중단하는 상황이 발생할 수 있으며 이로인해 고객유실이 있을 수 있다.** 
* 서버 교체나 업그레이드 시 서비스 이용이 어려울 수 있다. 

### 사용 예
* 한대의 서버에서 모든 데이터를 처리하므로 데이터 갱신이 빈번하게 일어나는 데이터베이스 서버에 적합하다.



## Scale out

* 비슷한 사양의 서버를 추가로 연결
* 서버에 걸리는 부하를 균등하게 해주는 **'로드밸런싱'이 필수적으로 동반되어야 함**
* 수평 스케일링(horizontal scaling)
* 데이터 분석처럼 복잡한 작업에 더 적합

### 장점

* 서버 한대가 장애로 다운되더라도 다른 서버로 서비스 제공이 가능하다.
* 용량, 성능 확장에 한계가 없다.
* 단일 서버에 작업이 쌓여서 멈춰있는 **병목현상**을 줄일 수 있다. 

### 단점

* 데이터 정합성을 유지하기 위한 설계, 구축비용이 크다.
* 데이터 정합성 이슈 발생 가능성이 커진다.


### 사용 예
* 데이터 변화가 적은 웹 서버는 스케일 아웃이 더 합리적일 수 있다.   
* 높은 병렬성을 실천하기 쉬운 경우, 정합성을 유지하기 쉬운 경우에 권장한다.   
* 네크워크 서버 설계 및 소프트웨어 변경 비용이 더 클 수 있기 때문에, <U>클라우드 환경 및 대규모 서비스 환경</U>에서 권장된다.   

참고 링크:
https://junghyungil.tistory.com/151   
https://bruno-jang.tistory.com/34   
https://souljit2.tistory.com/71   

