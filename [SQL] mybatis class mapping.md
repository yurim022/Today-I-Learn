
회사의 서비스 API를 개발하면서 mybatis를 접하게 되었다. 
result혹은 parameter를 클래스로 매핑시켰을때 필수로 필요한 요소가 무엇인지 알고 싶어 몇가지 테스트를 해보았다. 

## 필수1 : @Getter

```
@AllArgsConstructor
@NoArgsConstructor
//@Getter
@Builder
@JsonInclude(JsonInclude.Include.NON_NULL)
@JsonIgnoreProperties(ignoreUnknown = true)
public class Nick {
    private String storeNick;
}
```

정의한 도메인 클래스이다. 닉네임을 update하는 API에서 @Getter를 unable 해보았다. 
<img width="155" alt="image" src="https://user-images.githubusercontent.com/45115557/154390897-ce62c69e-3122-4df5-8462-fe1ae925a490.png">
 
 이렇게 하면 쿼리는 실행되지만 getter로 인자값을 가져 올 수 없어서 null 로 업데이트 된다. 
 
 ## 필수2 : @NoArgsContructor
 
 ```
 @AllArgsConstructor
//@NoArgsConstructor
@Getter
@Builder
@JsonInclude(JsonInclude.Include.NON_NULL)
@JsonIgnoreProperties(ignoreUnknown = true)
public class Nick {
    private String storeNick;
}
 ```
 
 
다음은 생성자를 disable 해보았다. 
<img width="810" alt="image" src="https://user-images.githubusercontent.com/45115557/154391143-97e5df66-ce75-44d8-8ee7-0648ee5f911d.png">
다음과 같은 에러가 발생한다. 
즉, mapper에서 jackso라이브러리를 사용하고 있으며 이때 NoArgsContructor가 사용됨을 알 수 있다. 

