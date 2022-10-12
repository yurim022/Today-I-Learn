# Java 컴파일 과정

<img width="601" alt="image" src="https://user-images.githubusercontent.com/45115557/195275286-8195423a-c2bc-418f-9b18-c71e661fb719.png">

1. 개발자가 .java 파일 생성 및 빌드(build)
2. 자바 컴파일러가 javac 명령어를 통해 바이트코드(.class) 생성 -> JVM을 위한 기계어
3. 클래스 로더(class loader)가 라이브러리 파일과 묶어 JVM 메모리 내에 로드
4. 실행엔진을 통해 컴퓨터가 읽을 수 있는 기계어로 해석(각 운영체제에 맞는)

<img width="756" alt="image" src="https://user-images.githubusercontent.com/45115557/195278671-2292073c-51c2-48a1-8df9-26d55eaea06d.png">

</br></br>
### JVM (Java Virtual Machine)

- 스택 기반으로 동작 
- 개발자가 OS 환경의 영향을 받지 않고 .java 파일을 각 OS에 맞게 해석 
- 가비지컬렉션을 통해 자동적인 메모리 관리   

</br>

### JIT

- 속도 이슈를 해결하기 위함 (인터프리터 언어의 단점 보완. 초장기 JAVA는 인터프리터 언어)
- 자주 읽게되는 소스에 대해 메모리에 해당 부분을 캐싱해두고, 읽어야 하는 시점에 덩어리째 반환
</br>

### Java가 컴파일러와 인터프리터 모두 사용하는 이유

- **OS Dependent**       
바로 기계어로 변환하는 컴파일러는 프로그램이 작성된 기계상에서 매우 효율적으로 동작하나, 기계 종류에 종속된다. 
Java 인터프리팅은 자바 컴파일러를 통해 생성된 클래스파일(byte)을 각각의 OS에 맞는 기계어로 변환하여 준다. 

- **보안적 장점**    
자바 byte 코드는 컴퓨터와 프로그램 사이에 별도의 버퍼 역할을 한다. 인터넷이나 기타 매체를 통해 신뢰할 수 없는 프로그램을 다운받거나 실행할 경우
자바 인터프리터를 사용함으로써 바이러스나 기타 악성 프로그램에 대응하는 가드 같은 보안계층에 의해 보호 될 수 있다. 

</br></br>
## 컴파일러 VS 인터프리터

#### 컴파일러 (compiler)
- 전체파일을 스캔하여 한꺼번에 번역
- 초기 스캔시간이 오래 걸리지만, 실행파일이 한번 만들어지고 나면 빠름
- 기계어 번역 과정에서 상대적으로 많은 메모리 사용 (obj 코드를 하나로 묶어서 실행 파일로 다시 만드는 과정 -링킹(linking) )
- 실행 전에 컴파일 단계에서 오류 검출 가능
- 대표적인 언어로 C,C++,Java(Java는 하이브리드 언어)


### 인터프리터 (interpreter)
- 프로그램 실행 시 한번에 한 문장씩 번역
- 한번에 한문장씩 번역 후 실행시키키 때문에 실행 시간이 느림
- 컴파일러와 같이 오브젝트 코드 생성과정이 없기 때문에 메모리 효율이 좋음
- 프로그램을 실행시키고 나서 오류를 발견하면 바로 실행 중지. 즉 실행 후에 오류를 알 수 있음
- 대표적인 언어로 Python, Ruby, Javascript 등이 있음



참고링크:   
https://rumor1993.tistory.com/90   
https://dev-coco.tistory.com/153   
https://velog.io/@tsi0521/Java%EB%8A%94-%EC%BB%B4%ED%8C%8C%EC%9D%BC%EB%9F%AC%EC%99%80-%EC%9D%B8%ED%84%B0%ED%94%84%EB%A6%AC%ED%84%B0-%EB%91%98-%EB%8B%A4-%EA%B0%80%EC%A7%84%EB%8B%A4   
https://velog.io/@jhur98/%EC%BB%B4%ED%8C%8C%EC%9D%BC%EB%9F%ACcompiler%EC%99%80-%EC%9D%B8%ED%84%B0%ED%94%84%EB%A6%AC%ED%84%B0interpreter%EC%9D%98-%EC%B0%A8%EC%9D%B4
