
## SecurityContext

먼저, Spring Context에 대해 알아보자. 
이는 접근 주체와 인증에 대한 정보를 담고 있는 context이다. 
#### SpringContext는 접근 주체인 Authentication을 담고 있으며, 현재 스레드 내에서 공유된다. 
정확하게는 SercurityContextHolder에 SecurityContext가 담겨있고, 이 Context를 통해 인증정보를 가져올 수 있다. 

![image](https://user-images.githubusercontent.com/45115557/156335705-c5a0506b-ba32-4d07-92dd-1cb39021f470.png)


## Authentication

인증 정보에 관한 부분이 정의된 인터페이스이다. 
- principal: 인증되는 주체의 ID
- Credentials: 주체가 정확한지 증명하는 것
- Authorities: 권한 (ADMIN, SUB 등)

## UserDetails

UserDetails의 구현체 정보는 Spring Security Context의 Authentication객체에 있다. 

```
public class CustomUser implements UserDetails {

    private String svcNo;
    private String email;
    private List<GrantedAuthority> authorities;

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return authorities;
    }

    @Override
    public String getPassword() {
        return null;
    }

    @Override
    public String getUsername() {
        return svcNo;
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }
}

```

정의한 CustomUser 객체를 컨트롤러에서 조회할 수 있다. 

```
@RequestMapping("/foo")
public String foo(@AuthenticationPrincipal CustomUser user) {
  ... // do stuff with user
  String email = user.getEmail();
}

```
Authentication객체의 Principal이 (여기에선 UserDetails 구현체인 CustomUser) @AuthenticationPrincipal 어노테이션으로 컨트롤러에서 매핑되는 것이다. 
이때 request에서 넘어온 인자들을 바인딩 해주는 것이 HandlerMethodArgumentResolver의 구현체인 AuthenticationPrincipalArgumentResolver이다. 




참고링크:
https://yoon0120.tistory.com/48
https://spring.io/guides/topicals/spring-security-architecture
https://sas-study.tistory.com/410
