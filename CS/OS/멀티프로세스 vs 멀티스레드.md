## 프로세스와 스레드
![image](https://user-images.githubusercontent.com/45115557/193408344-e3f975e0-60a3-4a4f-899c-00a60ba891ff.png)

프로세스는 운영체제로부터 자원을 할당받는 작업의 단위이고, 스레드는 프로세스가 할당받은 자원을 이용하는 실행의 단위이다. 기본적으로 ㅍ로세스마다 최소 1개의 스레드를 보유한다. 
* Code : 코드 자체를 구성하는 메모리 영역(프로그램 명령)
* Data : 전역변수, 정적변수, 배열 등
  * 초기화 된 데이터는 data 영역에 저장
  * 초기화 되지 않은 데이터는 bss 영역에 저장
* Heap : 동적 할당 시 사용 (new(), malloc() 등)
* Stack : 지역변수, 매개변수, 리턴 값 (임시 메모리 영역)



## Multi Process

### 장점
* 안정성이 높다.
* 하나의 프로세스가 죽더라도 다른 프로세스에 영향을 주지 않아 작업속도가 느려지는 손해는 생길 수 있으나, 문제는 발생하지 않는다.  (프로세스 간 메모리 구분, 독립적인 주소 공간을 가짐)

### 단점
* **context-switching 비용**이 높다. (**캐시 메모리 초기화**, 시간 소모)
* multi thread보다 **많은 메모리 공간과 CPU 시간을 차지**한다. 
* 프로세스 간 통신 비용이 스레드간 통신 비용보다 높다.


## Multi Thread

### 장점
* Code, Data, Heap 영역을 공유하기 때문에 (stack은 따로) 리소스 효율성이 높다. 
* 멀티 프로세스로 실행되는 작업을 멀티 스레드로 실행할 경우, **프로세스 생성/ 할당 비용을 줄여** 자원을 효율적으로 관리할 수 있다. 
* 프로세스 간 통신 비용보다 스레드간 통신 비용이 적다. 

### 단점
* 메모리를 공유하기 때문에 하나의 쓰레드가 자원을 수정하려 할때, **동기화 이슈**가 있다. ( critical section으로 관리 )



### Context Switching 이란
프로세스의 상태정보를 저장하고 복원하는 과정이다.    
CPU는 한번에 하나의 프로세스만 실행이 가능하기 때문에, 여러 프로세스를 돌아가면서 작업을 처리하면서 Context switching이 발생하게 된다. 



참고 링크:
https://wooody92.github.io/os/%EB%A9%80%ED%8B%B0-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4%EC%99%80-%EB%A9%80%ED%8B%B0-%EC%8A%A4%EB%A0%88%EB%93%9C/   
https://github.com/gyoogle/tech-interview-for-developer/blob/master/Computer%20Science/Operating%20System/Process%20vs%20Thread.md
