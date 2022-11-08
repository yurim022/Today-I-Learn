# PUT VS PATCH

put과 Patch는 공통적으로 `자원을 수정하는 HTTP 메서드`이다.    
흔히 멱등하다 멱등하지 않다에 대해 이야기할때 PUT과 PATCH를 예로 든다. 이 둘의 차이점에 대해 좀 더 자세히 알아보자. 

</br></br>

## PUT

PUT 메서드는 요청한 URI에 **payload에 있는 자원으로 대체하는 메서드**이다. 대체란 대상을 저장하는 것과 변경하는 것 두가지를 의미한다. 
* 요청한 URI 아래에 자원이 존재하지 않는 경우 : POST와 마찬가지로 새로운 자원으로써 저장하고 HttpStatusCode `201` 를 보낸다. 
* 요청한 URI 아래에 자원이 존재하는 경우 : 기존에 존재하던 자원을 적용하고 HttpStatusCode `200(ok)` 혹은 `204(no content)`를 보낸다. 

**PUT의 경우 전송하는 payload만으로 자원의 전체 상태를 나타낼 수 있어야 한다.** 만약 PUT의 정의대로 **전달받은 payload가 불안전한 상태로 전송된다면 일부 entity의 field값들은 null로 변경될 수 있다.** 

멱등이라는 것은 여러번 요청해도 같은 결과를 반환하는 것인데, PUT의 경우 자원 전체를 대체하기 때문에 여러번 요청해도 같은 결과가 된다. 즉, 멱등하다. 


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

</br></br></br></br>

## PATCH

PATCH는 **부분적인 수정을 적용**하기 위한 HTTP 메서드이다. PATCH는 부분 수정을 위한 데이터만 요청의 payload로 보내기 때문에 body를 받는 DTO를 별도로 만들어 주어야 한다. 
해당 DTO는 새로운 엔티티를 생성할 수 없고 오직 부분 수정을 위한 데이터로 사용된다. 

PATCH는 멱등하지 않다고 하는데, 사실 아래 예시와 같이 필드값을 수정한다면 계속 같은 결과가 반환된다. PATCH가 멱등하지 않은 경우는 
```
PATCH /users
[{ "op": "add", "username": "newuser", "email": "newuser@example.org" }]
```
위와 같이 특정 연산을 하는 경우이다. 필드명으로 접근하여 값을 변경하는 경우엔 PATCH도 반복된 요청에 대해 동일한 결과를 반환한다. 

</br>

### 예시

지하철 노선에 관련된 엔티티가 있다고하자. 해당 노선전오벵 노선이름과 배차간격만 변경 가능하다고 가정하자. 

```
public UpdateLineNameAndIntervalTime {
  
    private String name;
    private int intervalTime;
  
    public UpdateLineNameAndIntervalTime() {}
  
    public UpdateLineNameAndIntervalTime(String name, int intervalTime) {
        this.name = name;
        this.intervalTime = intervalTime;
    }
}
```

```
// LineService.java
...
@Transactional
public void updateLine(final Long id, final UpdateLineNameAndIntervalTime request) {
    Line line = lineRepository.findById(id)
        .orElseThrow(NoSuchElementException::new);
  
    line.update(request);
}
...
  
// Line.java
public void update(final UpdateLineNameAndIntervalTime request) {
    this.name = request.getName();
    this.intervalTime = request.getIntervalTime();
}

```

id로 해당 자원을 찾은 뒤, 자원의 일부만을 수정한다. 나머지 필드 값들은 변경되지 않는다. 

</br></br>

참고링크:      
https://tecoble.techcourse.co.kr/post/2020-08-17-put-vs-patch/   
https://developer-talk.tistory.com/745   
https://stackoverflow.com/questions/28459418/use-of-put-vs-patch-methods-in-rest-api-real-life-scenarios/39338329#39338329
