# CSRF & XSS

</br>

## CSRF (Cross Site Request Forgery)

사이트간 요청 위조를 뜻하며, 
해커가 사용자의 권한을 도용하여 사용자의 의지와는 무관하게 공격자가 의도한 행위 (modify, delete, register 등) 특정 웹사이트에 request 하게 만드는 것이다. 

</br>

### 발생시기

* 사용자가 해커가 만든 피싱 사이트에 접속한 경우
* 위조 요청을 전송하는 서비스에 사용자가 로그인을 한 경우
* 해커가 XSS 공격을 성공시킨 사이트일 경우(피싱사이트가 아니여도 CSRF 공격 가능)


</br>

### 시나리오

<img width="690" alt="image" src="https://user-images.githubusercontent.com/45115557/196342140-d49fc354-03e0-43b6-a0cb-b63b6850f8a1.png">

1. 공격자가 피싱 사이트에 공격 코드 삽입
2. 사용자는 공격자가 구성한 피싱 사이트 접속
3. 공격 코드가 실행되고, 사용자의 권한으로 제 3의 사이트에 요청을 보냄
4. 정보를 받아서 공격자가 권한으로 할 수 있는 요청 및 정보낚아채기


</br>

### 방어방법

**1. referer 검증**

서버에서 Referer 검증을 통해 승인된 도메인으로 요청시에만 처리하도록 한다. 

**2. Security Token 사용**

로그인을 할때 CSRF Token을 생성해서 세션에 저장한후, CSRF Token을 페이지 요청 태그의 hidden 값에 넣어준다.    
`ex) <input type="hidden" name="_csrf" value="${CSRF_TOKEN}" />`        
요청마다 CSRF Token을 같이 보내고, 서버는 이를 검증한다. 

**하지만 위 두 방법 모두, XSS 취약점이 있다면 공격 받을 수 있다.**


</br>

참고링크:   
https://velog.io/@asdfg5415/CSRFCross-Site-Request-Forgery-xo3sd276   
