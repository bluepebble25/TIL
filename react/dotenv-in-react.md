# React에서 .env 사용하기

## React 환경이 아닌 보통의 경우
보통은 .env 파일의 `키=값 `을 불러오기 위해 dotenv 라이브러리를 사용했을 것이다.

ES모듈의 import 방법
```js
import dotenv from "dotenv"

dotenv.config();
// .env가 루트 경로에 있지 않은 경우에는 예) dotenv.config({ path: '../config/.env' })
```
 
CommonJS 방법
```js
require("dotenv").config()
```

## React 환경
CRA(create-react-app)으로 React 프로젝트를 생성했다면 dotenv 라이브러리가 필요 없다. 이미 Webpack 안에 dotenv가 내장되어 있기 때문에 별도의 작업 없이 바로 `proccess.env.이름`으로 환경변수를 불러올 수 있다.

다만 조건이 있다.
- `.env` 파일을 프로젝트의 루트에 위치시킬 것 (src 아래가 아니라 프로젝트의 루트다!)
- 키 이름 앞에 `REACT_APP_`을 붙일 것

CRA로 생성된 프로젝트는 `REACT_APP_`으로 시작하는 환경변수만 읽어들일 수 있도록 설정이 되어 있다. 따라서 환경변수 키 이름 앞에 `REACT_APP_`을 붙여야 한다.
```js
// .env

REACT_APP_API_KEY=1234567abc
```
이렇게 `.env` 파일에 설정된 값을 React 프로젝트 어디에서나 `process.env.REACT_APP_API_KEY`로 불러올 수 있다.

### 활용 예
`Config.js`에서 아래와 같이 API_KEY를 모듈로 내보내고, 다른 파일에서 `API_KEY`를 `Config.js`로부터 import해서 사용한다.

```js
export const API_KEY = process.env.REACT_APP_API_KEY;
```