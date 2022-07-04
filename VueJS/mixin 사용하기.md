## mixin을 사용하는 이유

mixin은 한마디로 **재사용 가능한 메소드의 집합이다.** 
여러군데에서 사용되는 코드의 경우, 여러개의 컴포넌트를 만들기보다 하나의 믹스인을 재사용하여 반복되는 코드를 줄일 수 있다. 



## mixin 사용
**SomeVue.Vue**
mixin을 js나 vue로 구현한 뒤, mixins: []에 정의해준다. mixin은 여러개 선언할 수 있다. 

```
import myMixin from './mixin/myMixin'

export default(){
  mixins: [myMixin],	// 뷰에 사용할 믹스인을 선언해준다.
  data() {
  	return {}
  },
  method: {
  mounted(){
  this.someMixinMethods()
  }
  
  },
}
```

mixin은 컴포넌트와 같은 라이프사이클을 가지기 때문에 mounted(), created(), $router등을 사용할 수 있다.



참고: https://demain18-blog.tistory.com/48
