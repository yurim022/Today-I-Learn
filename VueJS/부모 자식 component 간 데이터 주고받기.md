## 부모 자식 Component 간에 데이터 주고받기

뭐가 부모고 뭐가 자식이냐!!

부모가 자식을 품고 있다고 보면 된다. 

Vue는 v-model아ㅣ라는 양방향 Data Binding Directive를 제공하고 있으나, Data 흐름을 추적하기 쉽지 않고, Debugging 플면에서 적합하지 않으므로 단방향 데이터 바인딩을 권장한다. 

이는 pros와 emit으로 구현 가능하다. 

- **pros**: **부모 컴포넌트에서 자식컴포넌트로 데이터를 전달할 때 사용**
- **emit**: **자식 컴포넌트에서 부모컴포넌트로 데이터 전달**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d1d23b36-5663-4fca-9999-45f4d17be1af/Untitled.png)
