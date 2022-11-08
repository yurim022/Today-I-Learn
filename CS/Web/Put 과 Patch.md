# PUT VS PATCH

put과 Patch는 공통적으로 자원을 수정하는 HTTP 메서드이다.    
흔히 멱등하다 멱등하지 않다에 대해 이야기할때 PUT과 PATCH를 예로 든다. 이 둘의 차이점에 대해 좀 더 자세히 알아보자. 

</br>

## PUT

PUT 메서드는 요청한 URI에 **payload에 있는 자원으로 대체하는 메서드**이다. 대체란 대상을 저장하는 것과 변경하는 것 두가지를 의미한다. 
* 요청한 URI 아래에 자원이 존재하지 않는 경우 : POST와 마찬가지로 새로운 자원으로써 저장하고 HttpStatusCode 201 를 보낸다. 
* 요청한 URI 아래에 자원이 존재하는 경우 : 기존에 존재하던 자원을 적용하고 HttpStatusCode 200(ok) 혹은 204(no content)를 보낸다. 

**PUT의 경우 전송하는 payload만으로 자원의 전체 상태를 나타낼 수 있어야 한다.** 

</br>

### 예시 

좋아요, 싫어요에 대한 정보를 가지고 있는 Like 엔티티가 있다. 

```
@Entity
public class Like {
  
  @Id
  private Long id;
  
  private Long articleId;
  
  private Long userId;
  
  private LikeType likeType;   //** liked or disliked
    
  ...
}
```

유저가 처음으로 좋아요(/싫어요) 를 눌렀다면 생성이 되어야 할 것이고, 기존에 누른 적이 있다면 다른 타입으로 수정되어야 한다.  

```
// LikeController.java
...
@PutMapping  //** PUT Method
public ResponseEntity<Void> updateLike(
    @RequestParam Long articleId,
    @LoginUser User user,
    @RequestBody LikeRequest request  //** LikeType
) {
    articleService.update(articleId, user.getId(), request.getLikeType());
    return ResponseEntity.noContent().build();
}
...
  
// LikeService.java
...
@Transactional
public void update(Long articleId, Long userId, LikeType likeType) {
    Like like = likeRepository.findByArticleIdAndUserId(articleId, userId)
      .map(l -> l.setType(likeType))
      .orElse(new Like(articleId, userId, likeType));

    likeRepository.save(like);
}
```

</br>

## PATCH

PATCH는 **부분적인 수정을 적용**하기 위한 HTTP 메서드이다. 




참고링크:      
https://tecoble.techcourse.co.kr/post/2020-08-17-put-vs-patch/   
https://developer-talk.tistory.com/745   
https://stackoverflow.com/questions/28459418/use-of-put-vs-patch-methods-in-rest-api-real-life-scenarios/39338329#39338329
