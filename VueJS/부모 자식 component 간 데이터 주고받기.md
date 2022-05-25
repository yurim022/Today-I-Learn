## 부모 자식 Component 간에 데이터 주고받기

뭐가 부모고 뭐가 자식이냐!!

부모가 자식을 품고 있다고 보면 된다. 

Vue는 v-model아ㅣ라는 양방향 Data Binding Directive를 제공하고 있으나, Data 흐름을 추적하기 쉽지 않고, Debugging 플면에서 적합하지 않으므로 단방향 데이터 바인딩을 권장한다. 

이는 pros와 emit으로 구현 가능하다. 

- **pros**: **부모 컴포넌트에서 자식컴포넌트로 데이터를 전달할 때 사용**
- **emit**: **자식 컴포넌트에서 부모컴포넌트로 데이터 전달**

<img width="756" alt="스크린샷 2022-05-24 오후 7 18 16" src="https://user-images.githubusercontent.com/45115557/170193182-11db8ef9-db5b-4b74-a9a8-3f1071b1dae5.png">


### 부모 컴포넌트

자식 컴포넌트를 components에 기재한 뒤 해당 데이터를 사용하고 있다. 

아래는 emit을 사용하는 방법이다.
@emit으로받아올event명="현재 컴포넌트에서 사용할 Event 명" 형태로 자식 컴포턴트의 데이트를 받을 수 있다. 
@close:popup, @chilData는 emit을  자식 컴토넌트로부터 받아온 값이다. 

```
<template>
  <div class="main-container">
    <popup
      :popup-val="popupVal"
      @close:popup="popupClose"
      @childData="childPopup"
    />
    <back-end-axios
      @licenseInfo="getLicenseInfo"
    />
  </div>
</template>

<script>
import popup from './component/licensePopup.vue'
import backEndAxios from './component/backEndAxios.vue'
export default {
  name: '',
  components: {
    popup,
    backEndAxios
  },
  data() {
    return {
      tableData: [],
      totalCount: 0,
      popupVal: false,
      childData: ''
    }
  },
  methods: {
    popupClose(popupVal) {
      alert('[indexVue] popupClose')
    },
    childPopup(childData) {
      alert('[indexVue] child to : ' + childData)
      this.childData = childData
    },
    getLicenseInfo(data) {
      this.tableData = data.items
      this.totalCount = data.totalCount
    }
  }
}
</script>
```


### 자식 컴포넌트

props에 부모컴포넌트로부터 받아올 데이터를 정의한다. 
emit은 다른 컴포넌트로 데이터를 보낼수 있는데 내부적으로 
this.$emit('@에서 작성한 emit 명칭', 현재 컴포넌트에서 전송할 Event나 Data 명) 로 정의한다. 

```
<template>
  <div>
    <el-dialog
      :visible.sync="popupVal"
    />
  </div>
</template>

<script>
export default {
  props: {
    popupVal: {}
  },
  data() {
    return {
    }
  },
  methods: {
    popupClose(popupVal) {
      this.popupVal = popupVal
      this.$emit('close:popup', popupVal)
    },
    depthChildPopup(childData) {
      this.$emit('childData', childData)
    }
  }
}
</script>
```



참고 링크:
[https://any-ting.tistory.com/41](https://any-ting.tistory.com/41)
[https://velog.io/@gillog/Vue.js-props-emit-부모-자식-Component-Data-주고-받기](https://velog.io/@gillog/Vue.js-props-emit-%EB%B6%80%EB%AA%A8-%EC%9E%90%EC%8B%9D-Component-Data-%EC%A3%BC%EA%B3%A0-%EB%B0%9B%EA%B8%B0)
