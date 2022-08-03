
# 동기 & 비동기 VS Blocking & Non-Blocking

두가지를 혼동하기 쉽지만, 두가지 개념은 초점이 다르며 직접적인 관련은 없다. 단지 조합하여 사용하는 것 뿐이다.    
**동기와 비동기는 프로세스의 수행 순서 보장에 대한 매커니즘이고, 블록킹과 논블록킹은 프로세스의 유휴 상태에 대한 개념이다.**


설명에 앞서, 먼저 두가지 용어를 짚고 넘어가자. 
### 제어권
제어권은 자신(함수)의 코드를 실행할 권리이다. 제어권을 가진 함수는 자신의 코드를 끝까지 실행한 후, 자신을 호출한 함수에게 돌려준다. 

### 결과값을 기다린다는 것
A 함수에서 B 함수를 호출했을 때, A 함수가 B 함수의 결과값을 기다리느냐 여부를 의미한다. 

## 동기

![image](https://user-images.githubusercontent.com/45115557/182599393-5a687dd0-c6f2-4ecb-9e93-a2c232a40e94.png)

호출하는 함수A가 호출되는 함수B의 작업 완료 후 리턴을 기다리거나, 바로 리턴받더라도 미완료 상태라면 **작업 완료 여부를 스스로 계속 확인하며 신경쓰면 synchronous**

## 비동기

![image](https://user-images.githubusercontent.com/45115557/182599431-90b24c1b-5682-49e5-a6b4-d365371a1e34.png)

함수A가 함수B를 호출할 때 *콜백 함수*를 함께 전달해서, 함수B의 작업이 완료되면 함께 보낸 콜백 함수를 실행한다.  
**함수A는 함수B를 호출한 후 작업 완료 여부에는 신경쓰지 않는다.**

---

## Blocking

![image](https://user-images.githubusercontent.com/45115557/182600543-d7d8020f-68f0-420f-8899-85631ca609a8.png)

A 함수가 B 함수를 호출하면, 제어권을 A가 호출한 B함수에 넘겨준다.  





참고링크 : 
https://inpa.tistory.com/entry/%F0%9F%91%A9%E2%80%8D%F0%9F%92%BB-%EB%8F%99%EA%B8%B0%EB%B9%84%EB%8F%99%EA%B8%B0-%EB%B8%94%EB%A1%9C%ED%82%B9%EB%85%BC%EB%B8%94%EB%A1%9C%ED%82%B9-%EA%B0%9C%EB%85%90-%EC%A0%95%EB%A6%AC   
https://velog.io/@nittre/%EB%B8%94%EB%A1%9C%ED%82%B9-Vs.-%EB%85%BC%EB%B8%94%EB%A1%9C%ED%82%B9-%EB%8F%99%EA%B8%B0-Vs.-%EB%B9%84%EB%8F%99%EA%B8%B0
