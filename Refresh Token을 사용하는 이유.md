## 상태 관리 방법들..

Cookie, Session, Jwt Token 모두 HTTP가 stateless 하기 때문에 로그인 정보 유지를 위해 사용한다. 
이에 대한 개념을 먼저 숙지하고 알아보자.   
세션은 [세션 동작 원리 - 쿠키와 세션의 관계](https://thecodinglog.github.io/web/2020/08/11/what-is-session.html) 글을 참고하자. 

</br>

## 토큰의 취약점

JWT 토큰을 통한 인증은 다양한 플랫폼에서 토큰만으로 인증할 수 있다는 장점이 있어 유용하게 사용되고 있다. 하지만 토큰 자체자 HTTPS를 사용하지 않는 환경이거나 기타 보안 취약점으로 인해 노출되었을때 탈취자가 사용자인 척 요청을 보내도 일일이 IP 주소 위치를 파악해서 비교하는게 아닌 이상, 구별할 방법이 없다. 때문에 Refresh 토큰을 사용해서 보안을 높일 수 있다. 

</br>

## Refresh Token 사용 sequence

1. Access Token의 유효기간은 짧게, Refresh Token의 유효기간은 길게 설정한다.
2. 사용자는 Access Token으로 인증하고 만료됬을 시 Refresh Token으로 새로운 Access Token, RefreshToken을 발급받는다.   
2-1. 공격자는 Access Token을 탈취하더라도 짧은 유효기간이 지나면 사용할 수 없다.    
2-2. 정상적인 클라이언트는 유효기간이 지나더라도 Refresh Token을 사용하여 새로 발급받을 수 있다.    

이러한 방식을 사용함으로써 Access Token이 탈취되더라도 그 피해를 줄이기 위해 토큰의 사용시간 자체를 줄이는 것이다. 

</br>

## Refresh Token이 탈취되었을 때

공격자가 Refresh Token 또한 탈취하였다면, 이를 통해 새로 AccessToken을 발급받을 수 있다. 이를 방지하기 위해 서버측에서도 검증 로직이 필요하다. 
    
가장 많이 사용되는 방법은 **데이터 베이스에 Access Token, Refresh Token 쌍을 저장** 하는 것이다.    
구체적으로 알아보자. 
</br>
### 정상적인 사용자    
기존의 Access Token으로 접근하며 서버측에서는 데이터베이스에 저장된 Access Token과 비교하여 검증   
</br>

### 공격자 
탈취한 Refresh Token으로 새로운 Access Token을 생성한 후 서버측에 전송   

#### CASE1.  데이터베이스에 저장된 토큰이 만료되지 않은 경우

Refresh Token이 탈취당했다고 가정하고 서버는 두 토큰 모두 만료시킴. 이 경우 정상적인 사용자의 토큰또한 만료되어 다시 로그인해야 하지만, 공격자의 토큰 역시 만료되었기 때문에 공격자는 정상적인 사용자의 리소스에 접근할 수 없다.    
</br>

#### CASE2.  데이터베이스에 저장된 토큰이 만료된 경우

서버는 데이터베이스에 저장된 Access Token과 공격자에게 받은 Access Token이 다른 것, 즉 토큰이 충돌이 일어나는 것을 확인하고 두 토큰을 모두 폐기한다. 같은 Refresh Token 으로 여러번 Access Token이 발급되는 것을 막기 위해 Refresh 토큰으로 Access Token을 발급받으면 Access, Refresh 모두 재발급 받는 것을 권장하고 있다.  
</br>


</br>

참고 링크:   
https://velog.io/@park2348190/JWT%EC%97%90%EC%84%9C-Refresh-Token%EC%9D%80-%EC%99%9C-%ED%95%84%EC%9A%94%ED%95%9C%EA%B0%80   
