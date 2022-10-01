# Blocking IO vs Non-Blocking IO

기본적으로 IO(입/출력) 작업은 Kernel에서만 수행할 수 있다. Process와 Thread는 커널에게 IO를 요청한다. 
입출력은 컴퓨터 내부/외부장치와 프로그램 간에 데이터를 주고받는 행위이다. 

## Blocking IO
 
 ### 진행순서
 
 1. Process(Thread)가 Kernel I/O 요청하는 함수 호출
 2. Kernel이 작업을 완료하면 작업 결과를 반환

Blocking IO의 경우 Thread는 작업을 중단한 채 대기한다. IO작업은 CPU자원을 거의 쓰지 않기 때문에 자원 낭비가 심하다. 
*Blocking 방식은 여러 Client가 접속하는 서버를 Blocking 방식으로 구현하는 경우 불리하다.*
다른 client가 진행중인 작업을 중지하면 안되므로, client 별로 별도의 Thread를 만들어야 하고 많아진 Thread의 컨텍스트 스위칭 횟수가 증가하게 된다. 
