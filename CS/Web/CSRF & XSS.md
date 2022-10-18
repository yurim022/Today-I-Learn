# CSRF & XSS


## CSRF (Cross Site Request Forgery)

사이트간 요청 위조를 뜻하며, 
해커가 사용자의 권한을 도용하여 사용자의 의지와는 무관하게 공격자가 의도한 행위 (modify, delete, register 등) 특정 웹사이트에 request 하게 만드는 것이다. 

### 발생시기

* 사용자가 해커가 만든 피싱 사이트에 접속한 경우
* 위조 요청을 전송하는 서비스에 사용자가 로그인을 한 경우
* 해커가 XSS 공격을 성공시킨 사이트일 경우(피싱사이트가 아니여도 CSRF 공격 가능)

### 시나리오

<img width="690" alt="image" src="https://user-images.githubusercontent.com/45115557/196342140-d49fc354-03e0-43b6-a0cb-b63b6850f8a1.png">

1. 공격자가 피싱 사이트에 공격 코드 삽입
2. 


참고링크:   
https://velog.io/@asdfg5415/CSRFCross-Site-Request-Forgery-xo3sd276   
