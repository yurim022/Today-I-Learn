
관리자 페이지를 개발하다 smart search를 사용해서 검색 기능을 구현하게 되었다.
ilike를 통해서 구현하였는데, 얘는 대소문자를 구분하지 않는다. 

## like 예시

```
SELECT string FROM string_collection WHERE string LIKE '_n%';
```
<img width="112" alt="image" src="https://user-images.githubusercontent.com/45115557/178936325-4d85c29f-28ac-45f4-ad17-8c42b25d52ad.png">


## ilike 예시

```
SELECT string FROM string_collection WHERE string ILIKE 'O%';
```
<img width="110" alt="image" src="https://user-images.githubusercontent.com/45115557/178936367-76d235eb-07b1-4f9e-a9e2-b7eb6a843e25.png">



참고링크: https://technobytz.com/like-and-ilike-for-pattern-matching-in-postgresql.html
