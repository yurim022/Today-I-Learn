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

## XSS (Cross Site Scription)

CSRF와 같이 웹 어플리케이션 취약점 중 하나로, 관리자가 아닌 권한이 없는 사용자가 웹 사이트에 스크립트를 삽입하는 공격 기법이다.  

</br>

### 시나리오

<img width="850" alt="image" src="https://user-images.githubusercontent.com/45115557/196346855-23675a45-1063-4e9a-812c-a8f642b04e6d.png">


1. 공격자가 악의적으로 스크립트를 삽입
2. 사용자가 감염된 페이지 열람
3. 쿠키를 공격자에게 전송
4. 탈취한 쿠키를 통해 세션 하이재킹 공격 (세션ID를 가진 쿠키로 사용자의 계정에 로그인)

</br>

### 공격 종류

**Persistent(or Stored) XSS**

<img width="855" alt="image" src="https://user-images.githubusercontent.com/45115557/196347731-6c2ea08c-b6e2-4c49-859d-da351ed4f5dd.png">

지속적으로 피해를 입는 유형으로, 삽입된 스크립트를 데이터베이스에 저장시키고 저장된 악성 스크립트가 있는 게시글 등을 열람한 사용자들은 악성 스크립트가 작동하면서 쿠키 탈취 및 리다이렉션 되는 공격을 받게 된다.

</br>

**Reflected XSS**

<img width="851" alt="image" src="https://user-images.githubusercontent.com/45115557/196347840-083f82c3-c1a0-4c37-9de8-6e186eefa45a.png">

사용자의 요청에 포함된 스크립트가 서버로부터 그대로 반사되어 응답 메세지에 포함되 브라우저에서 스크립트를 실행하게 되는 
공격 기법이다. 예를들어 사용자에게 입력받은 검색어를 그대로 보여주는 곳이나 사용자가 입력한 값을 에러 메세지에 포함하여 보여주는 곳에 악성 스크립트가 삽입되면 서버가 사용자의 입력값을 포함해 응답해 줄 때 해당 스크립트가 실행된다. 

</br>

### 방어방법

**1. 입출력 값 검증**

XSS Cheat Sheet 에 대한 필터 목록을 만들어 모든 Cheat sheet에 대한 대응을 가능하도록 사전에 대비한다.   
XSS 필터링을 적용 후 스크립트가 실행되는지 직접 테스트 과정을 거쳐볼 수도 있다.

**2. XSS 방어 라이브러리, 확장앱**

Anti XSS 라이브러리를 제공해주는 회사가 많다. 이 라이브러리는 서버단에서 추가하며, 사용자들은 각자 브라우저에서 악성 스크립트가
실행되지 않도록 확장앱을 설치하여 방어할 수 있다. 

**3. 웹 방화벽**

웹 방화벽은 웹 공격에 특화된 것으로, 다양한 injection을 한꺼번에 방어할 수 있다. 

**4. CORS, SOP 설정**

CORS(Cross-Origin Resource Sharing), SOP(Same-Origin-Policy) 를 통해 리소스의 Source를 제한한다. 
웹 서비스상 취약한 벡터에 공격 스크립트를 삽입 할 경우, 치명적인 공격을 하기 위해 스크립트를 작성하면 입력값 제한이나 기타 요인 때문에 공격 성공이 어렵다. 그러나 공격자의 서버에 위치한 스크립트를 불러올 수 있다면 이는 상대적으로 쉬워진다. 
때문에 CORS, SOP를 활용해 지정된 도메인이나 범위가 아니라면 리소스를 가져올 수 없게 제한해야 한다. 

**5. 쿠키 HttpOnly 옵션 활성화**

HttpOnly를 활성화하지 않으면 스크립트를 통해 쿠키에 접근할 수 있어 Session Hijacking에 취약해질 수 있다. HttpOnly 옵션을 
통해 악의적인 클라이언트가 쿠키에 저장된 정보(Session Id,Token)에 접근하는 것을 차단할 수 있다. 
더불어 LocalStorage에 세션 ID와 같은 민감한 정보를 저장하지 않는 것이 중요하다. 

cf) Secure은 웹브라우저와 웹서버가 Https로 통신하는 경우에만 웹브라우저가 쿠키를 서버로 전송하는 옵션

</br>

참고링크 및 이미지 출처:   
https://velog.io/@asdfg5415/CSRFCross-Site-Request-Forgery-xo3sd276   
https://overcome-the-limits.tistory.com/m/510   
https://github.com/gyoogle/tech-interview-for-developer/blob/master/Web/CSRF%20%26%20XSS.md   
