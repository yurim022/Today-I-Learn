# Blocking IO vs Non-Blocking IO

기본적으로 IO(입/출력) 작업은 Kernel에서만 수행할 수 있다.(context-switch) Process와 Thread는 커널에게 IO를 요청한다.    
참고로 입출력은 컴퓨터 내부/외부장치와 프로그램 간에 데이터를 주고받는 행위이다. 

### Buffer VS Stream
간단하게 Buffer는 용기가 찰 때까지 보관했다가 한꺼번에 나르는 것이고, Stream은 순차적으로 계속 나르는 것이다. Buffer는 주로 양방향으로 데이터 전달이 가능한 Channel 방식에 사용되고 blocking/non-blocking모두 가능하고, Stream은 read 혹은 write 단방향만 지원되며  blocking 방식으로 진행된다. 

## Blocking IO
 
### Synchronous blocking I/O
 ![image](https://user-images.githubusercontent.com/45115557/193399947-59a14c8c-2ed8-469a-93d3-114b8db0496d.png)
출처: [IBM Developer](https://developer.ibm.com/articles/l-async/)


Blocking IO의 경우 Thread는 작업을 중단한 채 대기한다. IO작업은 CPU자원을 거의 쓰지 않기 때문에 자원 낭비가 심하다.    
**Blocking 방식은 여러 Client가 접속하는 서버를 Blocking 방식으로 구현하는 경우 불리하다.**
다른 client가 진행중인 작업을 중지하면 안되므로, client 별로 별도의 Thread를 만들어야 하고 많아진 Thread의 컨텍스트 스위칭 횟수가 증가하게 된다. 

### Asynchronous Blocking I/O
![image](https://user-images.githubusercontent.com/45115557/193401394-0a61d59d-cacb-4dff-8ab9-5eeeb0334701.png)


## Non-Blocking IO

### Synchronous non-blocking I/O
![image](https://user-images.githubusercontent.com/45115557/193401409-5e97b591-8104-46ca-81fe-a484c08008e7.png)
출처: [IBM Developer](https://developer.ibm.com/articles/l-async/)

1. User Process가 recvfrom 함수 호출 (커널에게 해당 socket으로부터 data를 받고 싶다 요청)
2. Kernel은 곧바로 recvBuffer를 채워서 보내지 못하므로 "EWOULDBLOCK"을 return
3. Non-Blocking은 I/O 작업이 진행되는 동안 다른 작업을 진행 할 수 있음
4. recvBuffer에 user가 받을 수 있는 데이터가 있는 경우. Buffer로부터 데이터를 복사하여 받아옴 (이떄 recvBuffer는 커널이 가지고 있는 메모리에 적재되어 있으므로 메모리간 복사로 인해 I/O보다 훨씬 빠른 속도로 data를 받아올 수 있음)
5. recvfrom 함수는 빠른 속도로 data를 복사한 후 , 복사한 data의 길이와 함께 반환


### ASynchronous non-blocking I/O

비동기는 I/O 처리가 완료된 타이밍으로 결과를 회신한다.
![image](https://user-images.githubusercontent.com/45115557/193401266-7591aae7-7b79-4f2b-886b-919b864292fc.png)
출처: [IBM Developer](https://developer.ibm.com/articles/l-async/)

참고링크: 
https://github.com/gyoogle/tech-interview-for-developer/blob/master/Computer%20Science/Network/%5BNetwork%5D%20Blocking%20Non-Blocking%20IO.md   
https://boomrabbit.tistory.com/145   
https://developer.ibm.com/articles/l-async/   
https://limdongjin.github.io/concepts/blocking-non-blocking-io.html#ibm-%E1%84%8B%E1%85%A1%E1%84%90%E1%85%B5%E1%84%8F%E1%85%B3%E1%86%AF
