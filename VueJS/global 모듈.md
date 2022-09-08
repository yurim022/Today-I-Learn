### 의문의 코드

```
const loading = this.$loading({ lock: true, text: this.$t('loading.data'), spinner: 'el-icon-loading' });
```

관리자 포털을 개발하다 다음과 같은 코드를 발견했다. 현재 프로젝트에서는 element-plus를 사용하고 있는데 로딩할때 element-plus에서 아이콘을 띄워주기 위해 사용한 것이다. 


```
this.$axios
        .get(`/data`, {
          params: {
            offset: (this.curPage - 1) * this.pageSize,
            limit: this.pageSize,
          },
        })
        .then(res => {
            this.resultList = res.data;
        })
        .catch(err => {
          this.$alert(
            this.errorResponse(err).code ? this.errorResponse(err).message : this.defaultErrorMessage(),
            this.$t('error.retrieve.list'),
          );
        })
        .finally(() => {
          loading.close();
        });
    }

```
데이터를 다 불러온뒤, finally 문에서 ```loaing.close()``` 로 종료시켜주면 된다. 


### global.d.ts

```
declare module '@vue/runtime-core' {
  interface ComponentCustomProperties {
    $loading: typeof import('element-plus')['ElLoadingService']
  }
}
```

global.d.ts 파일에 다음과 같이 $loading이 명시되어 있었다. 

## .d.ts 파일

global.d.ts 파일이 확장자가 좀 특이하다.  .d.ts 파일은 무엇일까
* 구현부가 아닌 선언부만을 작성하는 용도의 파일
* JS코드로 컴파일 불가
* 최상위에 존재하는 변수, 상수, 함수, 클래스, 네임스페이스의 선언 앞에는 반드시 ``` declare ``` 또는 ``` export``` 가 붙어야 한다.
* 파일에 작성되는 ```declare namespace``` 블록과 ```declare module``` 블록의 필드들에는 ```export``` 키워드가 기본으로 붙기 때문에 굳이 붙여줄 필요가 없다. 


참고링크:
https://it-eldorado.tistory.com/127

