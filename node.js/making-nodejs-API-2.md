# nodejs API 만들기(2) - 리팩토링
[이전 글 - nodejs API 만들기(1)](./making-nodejs-API-1.md)

## 도입
리팩토링의 각 단계마다 미리 작성해놓았던 테스트 코드를 실행해가며 진행해보자. 테스트 코드가 잘 작동하면 안심하고 다음 단계의 리팩토링을 진행해도 좋다. 테스트 코드를 작성하면 좋은 점이 바로 이럴 때이다.

## 1. 파일 분리
api 폴더를 만들고 그 안에 users 폴더를 만든다. api/users 안에 index.js, user.ctrl.js, user.spec.js 파일을 생성한다.

- 상위의 index.js - 서버를 구동하는 시작점 역할
- api/users/index.js - 라우팅 담당. 라우팅 함수에 user.ctrl.js의 로직을 바인딩해주는 역할
- user.ctrl.js - 라우팅 함수에서 라우팅 로직만을 따로 분리한 것.
- user.spec,.js - 테스트 코드

## 2. 라우터 분리
1. 상위의 index.js에서 유저 정보를 담고 있는 `users 배열`과 app.get(), app.post, ... 형식을 한 `라우팅 함수`를 `api/user/index.js`로 옮긴다.
2. 라우팅 클래스의 인스턴스를 router라는 이름으로 생성한다.
3. app.get() 형태를 한 라우팅 함수에서 app을 전부 router로 대체한다.
4. router를 export한다.
5. index.js로 가서 `.api/users` 모듈을 불러온 다음 (index.js까지는 안써도 된다), 미들웨어를 추가하는 것과 같은 방식으로 추가해준다. - `app.use('/users', user);`
6. api/users/index.js로 가서 `/users`, `/users/:id` 형태의 경로를 전부 `/`, `/:id`로 바꿔준다. 그 이유는 이미 라우터를 index.js의 미들웨어에 /users라는 경로로 추가했기 때문에 그 아래에서는 /users를 쓰지 않아도 되기 때문이다.

```js
// api/users/index.js
const express = require('express');
const router = express.Router();

let users = [
  {id: 1, name: 'James'},
  {id: 2, name: 'Amelia'},
  {id: 3, name: 'Luna'}
];

router.get('/', (req, res) => {
  req.query.limit = req.query.limit || 10;
  const limit = parseInt(req.query.limit, 10);
  if(Number.isNaN(limit)) {
    return res.status(400).end();
  }
  res.json(users.slice(0, limit));
});

// ...

module.exports = router;
```

```js
// index.js
// 이 두 코드를 적절한 위치에 추가해준다.
const user = require('./api/user');

app.use('/users', user);
```

## 3. 라우팅 로직 분리
1. `api/user/index.js`의 라우팅 함수에서 두 번째 파라미터인 로직 함수들을 모두 `user.ctrl.js`로 옮겨준다. 그리고 각각에 이름을 붙여준다.
2. export로 함수들을 내보낸다.
3. api/user/index.js에서 ./user.ctrl을 불러오고, 라우팅 함수에 로직을 ctrl.index와 같이 바인딩해준다.
4. api/user/index.js에 있던 유저 정보를 담은 users 배열은 라우터에서 사용하지 않고 로직에서 사용하므로 user.ctrl.js의 상단으로 옮기자.

```js
// api/user/index.js
const ctrl = require('./user.ctrl');

router.get('/', ctrl.index);
router.get('/:id', ctrl.show);
// ...
```

```js
// api/user/user.ctrl.js
let users = [
  {id: 1, name: 'James'},
  {id: 2, name: 'Amelia'},
  {id: 3, name: 'Luna'}
];

const index = (req, res) => {
  req.query.limit = req.query.limit || 10;
  const limit = parseInt(req.query.limit, 10);
  if(Number.isNaN(limit)) {
    return res.status(400).end();
  }
  res.json(users.slice(0, limit));
};
// ...

module.exports = {
  index, show, destroy, create, update
};
```

## 4. 테스트 코드 이동
1. index.spec.js에 있던 테스트 코드를 api/user/user.spec.js로 옮겨보자. 그 다음 index.spec.js는 삭제한다.
2. 그리고 app의 위치가 바뀌었으므로 `const app = require('../../');`처럼 경로를 바꿔준다. user 폴더에서 두 번 밖으로 나가면 index.js가 있기 때문에 그렇게 바꿔준 것이다. index.js까지는 쓰지 않아도 된다.
3. package.json의 스크립트 중 test를 수정된 경로로 바꿔주자. (Windows 기준)
    ```json
    "test": "node ./node_modules/mocha/bin/mocha api/user/user.spec.js"
    ```

## 5. 테스트 환경 개선
테스트를 돌릴 때 아래와 같은 코드로 인해 테스트 결과 문구와 함께 서버 프로그램이 발생시키는 로그가 함께 찍히게 된다.
둘이 섞여서 화면에 보이면 가독성이 좋지 못하므로 테스트를 동작시킬 때에는 서버 프로그램이 로그를 찍지 않도록 하려고 한다.
```js
app.use(morgan('dev'));

app.listen(3000, () => {
  console.log('Server is running on port 3000');
})
```
이를 환경변수를 통해 조정할 수 있다.

### 환경 변수
환경변수란 운영체제 단에 저장해놓는 변수라고 생각하면 된다. 우리가 어떨 때 환경변수를 사용하는지 예시를 들어 설명해보겠다.

OS에 NODE_ENV라는 이름의 환경변수를 등록했다고 하자. 일반 사용자는 NODE_ENV의 값으로 production을, 개발자는 development을 등록했다.

이제 환경변수를 이용해 코드를 작성하면 다음과 같이 값에 따라 다른 동작을 하도록 할 수 있다.
- `if(NODE_ENV === 'production')`일 때 - 일반 사용자 전용 코드 실행
- `if(NODE_ENV === 'development')`일 때 - 개발할 때 필요한 코드 실행

### 환경 변수 등록방법
방법 1) Windows에서 영구적인 방법
- PC의 속성 -> 고급 시스템 설정 -> 환경변수에 들어가 직접 환경변수를 등록한다.

방법 2) cmd 창을 닫기 전까지 환경변수 유지하기
- SET 키=값 (Windows 기준)
- export 키=값 (리눅스 기준)

방법 3) node 프로세스 실행마다 환경변수 등록하기
- node 명령어 앞에 `키=값`처럼 환경변수를 같이 입력한다.
- 예) `API_KEY=123 node`로 node 실행하면 그 프로세스 동안은 환경변수가 살아있다.
  `process.env`라는 자바스크립트 객체로 `process.env.API_KEY`처럼 참조할 수도 있음.

### 활용편
1. 테스트 스크립트에 환경변수 등록하기
   
    Windows 환경에서는 node 프로그램을 실행하면서 환경변수를 주는 것은 되지만, node index.js처럼 파일을 실행하면서 환경변수를 전달할 수는 없다.
    그래서 `cross-env`라는 모듈을 설치해야 한다.
    ```
    npm install cross-env -D
    ```

    그리고 test 스크립트를 이렇게 바꿔준다.
    ```json
    "test": "cross-env NODE_ENV=test mocha api/user/user.spec.js"
    ```

2. 테스트 실행시 콘솔에 로그를 남기는 부분을 if문 처리 하기
    ```js
    if(process.env.sNODE_ENV !== 'test') {
      app.use(morgan('dev'));
    }
    ```

3. 서버의 시작점을 bin/www.js 파일에 분리하기

    테스트 코드의 superTest가 서버를 구동해주기 때문에 index.js의 `app.listen(...)`은 테스트 시에 필요없다.
    하지만 이것을 삭제하면 정작 테스트가 아니라 그냥 서버를 구동할 때 문제가 생기므로 이것을 다른 곳에 분리시키자.

    bin 폴더를 생성하고 그 아래에 www.js라는 파일을 만든다.
    그리고 다음과 같이 서버 구동 부분을 옮겨주고 index.js를 app이란 이름으로 불러오면 된다.
    이제 테스트 코드를 실행할 때 서버 구동 로직이 화면에 보이는 일은 없다.
    ```js
    // bin/www.js
    const app = require('../index.js');

    app.listen(3000, () => {
      console.log('Server is running on port 3000');
    });
    ```

4. start 스크립트 수정하기
  
    서버 시작점을 bin/www.js로 바꿔준다.
    ```json
    "start": "node bin/www.js"
    ```

## Reference
- https://rangsub.tistory.com/111
- https://www.daleseo.com/js-node-process-env/