# 단위 테스트 작성 방법

*망나니 개발자님 블로그 글을 참고하여 정리한 글입니다. *

## 필요 라이브러리


* JUnit5
* AssertJ ; Junit에서 제공하는 메소드가 있지만 ex) assertEquals() 가독성이 떨어진다. 


## given / when / then 패턴

단위테스트에서 사용하는 패턴이다.
* given(준비) : 어떤 데이터가 준비되었을 때,
* when(실행) : 어떤 함수를 실행하면
* then(검증) : 어떤 결과가 나와야 한다. 

   추가적으로 verify로 어떤 메소드가 몇번 호출되었는지 검사하기도 하지만, 실용성이 낮아 메서드 호출 횟수가 중요한 테스트에서만 선택적으로 사용하면 된다고 한다!
   
```
@Displayname("testcode 메서드별 네임을 명시하고 싶을때")
@Test
void testExample(){

//given

//when

//then

}
```
   기본적으로 이러한 구조로 단위 테스트가 진행된다. 

















참고 링크:   https://mangkyu.tistory.com/144   
