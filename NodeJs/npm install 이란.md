# npm install 이란

현재 프로젝트에서 springboot와 view를 함께 배포하는 요구사항이 있다.   
pom.xml에 보니 다음과 같이 npm install을 실행하는 부분이 있었다.

```xml
<configuration>
	<skip>${skip.local.npm.build}</skip>
	<executable>npm</executable>
	<arguments>
		<argument>install</argument>
	</arguments>
	<workingDirectory>${basedir}/view</workingDirectory>
</configuration>

```

## npm install과 패키지

npm install의 동작은 크게 두가지로 나눌 수 있다. 
1. 패키지명을 명시해 특정 패키지를 설치  ex) `npm install express`
2. 패키지명을 명시하지 않고 `package.json` 파일의 의존성을 설치하는 동작 ex) `npm install`

1번의 경우 express 모듈이 설치되고, 2번의 경우 package.json의 의존성 패키지들이 일괄적으로 설치된다. 

#### package.json 예시

```json

 "dependencies": {
    "@element-plus/icons-vue": "^1.1.4",
    "axios": "^0.27.2",
    "core-js": "^3.8.3",
    "element-plus": "^2.2.0",
    "jsencrypt": "^3.2.1",
    "moment": "^2.29.3",
    "node.js": "^0.0.1-security",
    "querystring": "^0.2.1",
    "router": "^1.3.7",
    "vue": "^3.2.13",
    "vue-i18n": "^9.2.0-beta.35",
    "vue-json-viewer": "^2.2.22",
    "vue-router": "^4.0.13",
    "vuex": "^4.0.2"
  },
  "devDependencies": {
    "@babel/core": "^7.12.16",
    "@babel/eslint-parser": "^7.12.16",
    "@kazupon/vue-i18n-loader": "^0.5.0",
    "@vue/cli-plugin-babel": "~5.0.0",
    "@vue/cli-plugin-eslint": "~5.0.0",
    "@vue/cli-service": "~5.0.0",
    "eslint": "^7.32.0",
    "eslint-plugin-vue": "^8.0.3"
  },
  ..(중략)

```

</br>

### 특정 패키지 설치

특정 패키지를 설치할 때는 크게 두 가지 옵션으로 구분된다. 

*  프로젝트 구동 시 필요한 `dependencies` 목록에 추가될 겨우 `npm install (프로젝트명)` 으로 설치
*  개발 단계에서면 필요한 `devDependencies` 목록에 추가될 경우 `npm install -D (프로젝트명) 으로 설치

이때 `-D`와 같은 접미어를 *플래그* 라고 부른다. 

#### 플래그
* `-P` , `--save-prod` (default) : 패키지를 설치하고 프로젝트의 `dependencies` 목록에 추가
* `-D`, `--save-dev` : 패키지를 설치하고 프로젝트의 `devDependencies` 목록에 추가
* `-g` : 패키지를 프로젝트가 아닌 시스템의 `node_modules` 폴더에 설치


참고링크:   
https://c17an.netlify.app/blog/node.js/npm-install-%EC%A0%95%EB%A6%AC/article/
