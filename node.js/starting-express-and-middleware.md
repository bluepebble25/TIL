# Express 시작하기 및 미들웨어
목차
- [Express 시작하기 및 미들웨어](#express-시작하기-및-미들웨어)
  - [설치](#설치)
  - [어플리케이션 생성하기](#어플리케이션-생성하기)
  - [미들웨어 만들기](#미들웨어-만들기)
    - [미들웨어 함수 형태](#미들웨어-함수-형태)
    - [어플리케이션 레벨 미들웨어](#어플리케이션-레벨-미들웨어)
    - [라우터 레벨 미들웨어](#라우터-레벨-미들웨어)
    - [오류 처리 미들웨어](#오류-처리-미들웨어)
  - [외부 미들웨어 사용하기](#외부-미들웨어-사용하기)

## 설치
- `npm install express`를 하면 된다.
- 그러면 현재 폴더의 node_modules에 express가 설치된다.
- 매번 `node index.js`를 하는 게 귀찮다면 `npm init`을 해서 package.json 파일을 생성하고,
- package.json의 `scripts` 부분에
  ```json
  "scripts": {
    "start": "node index.js"
  }
  ```
  과 같이 start 명령어를 받으면 할 행동을 추가해주자. 그러면 앞으로는 `node index.js` 대신 `npm start`만으로 서버가 시작될 것이다.

## 어플리케이션 생성하기
```js
const express = require('express');
const app = express();
```
- `require('express')`로 express 모듈을 불러오고,
- `express()`로 express의 인스턴스를 생성한다. 생성된 express 인스턴스를 어플리케이션이라고 부른다.

어플리케이션의 역할은 서버에 필요한 미들웨어를 호출하는 것이다. 미들웨어는 request와 response라는 http 전송을 중개하는 역할을 하는 함수라고 생각하면 된다. 결국 ***미들웨어 = 라우터***다. express 어플리케이션은 미들웨어 함수를 호출하는 것의 연속으로 이루어져 있다고 할 수 있다.

## 미들웨어 만들기
미들웨어는
- 어플리케이션 레벨 미들웨어
- 라우터 레벨 미들웨어

로 나눌 수 있다.

둘의 차이점을 React에 비유해보자면, 어플리케이션 레벨 미들웨어는 React의 App.js에 바로 작성한 컴포넌트이고, 라우터 레벨 미들웨어는 로고, 검색, 탭 컴포넌트를 App.js에 올리기 위한 용도로 묶은 네비게이션 컴포넌트라고 할 수 있다.

라우터 레벨 미들웨어는 express.Router()에 여러 미들웨어를 바운드해서 한꺼번에 어플리케이션에 올릴 때 사용한다.

### 미들웨어 함수 형태
```js
app.get('/', function(req, res, next) {
  //...
  next();
});
```
- 이 미들웨어 함수는 app에 바로 올라와있고, '/' 경로로 GET 요청을 받았을 때 실행된다.
- `function(req, res, next) {...}` - 미들웨어 함수
- `req, res` - node.js의 req와 res 객체를 래핑한 것이다. 따라서 이것을 로직에 이용하는 법은 기존과 같고 몇 가지 프로퍼티와 메소드가 추가된 것 뿐이다.
- `next` - next()를 작성하지 않으면 다음 미들웨어로 넘어가지 않는.

### 어플리케이션 레벨 미들웨어
`app.use()`와 `app.get` (get 대신 put, post 등도 가능)의 차이는 다음과 같다.
- `app.use()` - 모든 종류의 HTTP 요청에 응답함
- `app.METHOD()` - 해당하는 종류의 METHOD 요청에만 응답함

```js
const express = require('express');
const app = express();

function myLogger(req, res, next) {
  console.log('LOGGED');
  next();
}

app.use(myLogger);
app.get('/', function(req, res, next) {
  console.log('Hi!');
  next();
});
app.use('/users', function(req, res, next) {

});

app.listen(3000, function() {
    console.log("Server is running");
});
```

- `app.use(myLogger);`
  - `app.use()`는 모든 종류의 HTTP 메소드에 대해 응답한다.
  - 그리고 `app.use('/users', function)`처럼 경로를 지정해주지 않았기 때문에 모든 http 요청에 대해 myLogger를 실행한다.
- `app.get('/', function)` - `/` 경로로 온 GET 요청에 대해 미들웨어 함수를 실행한다.
- `app.use('/users', function)` - `/users` 경로로 온 모든 METHOD의 요청에 대해 미들웨어 함수를 실행한다.

### 라우터 레벨 미들웨어
app 대신 미들웨어를 express.Router()에 바운드한 것 말고는 작성 방법은 비슷하다.  
작성된 router를 한꺼번에 app에 올릴 수 있다.
```js
const express = require('express');
const app = express();
const router = express.Router();

router.use('/', function(req, res, next) {
  console.log('router1');
  next();
});

router.get('/', function(req, res, next) {
  console.log('router2');
  next();
});

app.use('/', router);

app.listen(3000, function() {
  console.log("Server is running");
});
```

결과)
> router1  
> router2

### 오류 처리 미들웨어
미들웨어는 순차적으로 처리되므로, 오류 처리 미들웨어를 만났다는 것은 그 위의 요청들을 처리하지 못했다는 뜻이다. 따라서 맨 아래에 위치시켜야 한다.

오류 처리 미들웨어는 일반 미들웨어와 다르게 `err, req, res, next` 네 개의 파라미터를 가진다.

```js
const express = require('express');
const app = express();

function commonmw(req, res, next) {
    console.log('common');
    next(new Error('error occurred'));
}

function errmw(err, req, res, next) {
    console.log(err.message);
    // 에러 처리 로직
    next();
}

app.use(commonmw);
app.use(errmw);

app.listen(3000, function() {
  console.log("Server is running");
});
```

## 외부 미들웨어 사용하기
npm으로 다른 사람들이 배포한 미들웨어를 설치해 사용할 수도 있다.

```
npm install morgan
```
으로 morgan 모듈을 다운받는다. morgan은 로그를 남기는 것을 간편하게 해준다. console.log()로 로깅함수를 작성한다면 일일이 로그를 보고 싶은 항목을 (예- `req.url`, `req.status`) 작성해야 하는데, morgan은 `morgan()`에 옵션을 넣는 것만으로도 간단히 원하는 사항들을 로깅해준다.  
아래의 예시에서는 `morgan('dev')`과 같이 `dev` 옵션을 줬는데, 이것은 개발 편리를 위해 상태 코드에 색깔을 입힌 로그를 화면에 보여준다.
```js
const express = require('express');
const morgan = require('morgan');
const app = express();

function logger(req, res, next) {
    console.log('I am logging');
    next();
}
function logger2(req, res, next) {
    console.log('I am logging too');
    next();
}

app.use(logger);
app.use(logger2);
app.use(morgan('dev'));

app.listen(3000, function() {
  console.log("Server is running");
});
```
결과
> I am logging  
> I am logging too  
> GET / 404 3.327 ms - 139 (404 상태코드라 이 부분이 노란 글자로 나온다)