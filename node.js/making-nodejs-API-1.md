# nodejs API 만들기(1)
이 글의 내용은 [테스트주도개발(TDD)로 만드는 NodeJS API 서버](https://www.inflearn.com/course/%ED%85%8C%EC%8A%A4%ED%8A%B8%EC%A3%BC%EB%8F%84%EA%B0%9C%EB%B0%9C-tdd-nodejs-api#) 강좌를 듣고 정리한 내용임을 밝힙니다. / [github](https://github.com/jeonghwan-kim/lecture-node-api)

이 git 저장소를 보는 사람이 있을 것 같지는 않지만, public repo이기도 하고 유료 강의이므로 코드의 상당부분을 생략하고 제가 이해한 로직 위주로 작성했습니다.

사용자 목록을 조회/삭제/수정/추가하는 API

> 목차
> 1. [조회](#1-조회)
> 2. [삭제](#2-삭제)
> 3. [추가](#3-추가)
> 4. [수정](#4-수정)

## 1. 조회
### `GET /users`
(1) 성공시
- 유저 객체를 담은 배열을 응답한다.
- 최대 limit 개수만큼 응답한다.

(2) 실패시
- limit가 숫자형이 아니면 400을 응답한다.

### `GET /users/:id`
(1) 성공시
- id가 1인 유저 객체를 반환한다.

(2) 실패시
- id가 숫자가 아닐 경우 400을 응답한다.
- id로 유저를 찾을 수 없을 경우 404를 응답한다.

### 테스트 코드
위의 API 명세서를 만족시키기 위해서 먼저 그것을 테스트할 수 있는 코드부터 만들어보자.

```js
const app = require('./index');
const request = require('supertest');
const should = require('should');

describe('GET /users는', () => {
  describe('성공시', () => {
    it('유저 객체를 담은 배열로 응답한다.', (done) => {
      request(app)
          .get('/users')
          .end((err, res) => {
            res.body.should.be.instanceOf(Array);
            done();
          });
    });

    it('최대 limit 개수만큼 응답한다.', (done) => {
      request(app)
          .get('/users?limit=2')
          .end((err, res) => {
            res.body.should.have.lengthOf(2);
            done();
          });
    });
  })
  describe('실패시', () => {
    it('limit가 숫자형이 아니면 400을 응답한다.', (done) => {
      request(app)
          .get('/users?limit=two')
          .expect(400)
          .end(done);
    });
  })
});

describe('GET /users/1은', () => {
  describe('성공시', () => {
    it('id가 1인 유저 객체를 반환한다.', (done) => {
      request(app)
          .get('/users/1')
          .end((err, res) => {
            res.body.should.have.property('id', 1);
            done();
          })
    });
  });
  describe('실패시', () => {
    it('id가 숫자가 아닐 경우 400을 응답한다.', (done => {
      request(app)
          .get('/users/one')
          .expect(400)
          .end(done);
    }));
    it('id로 유저를 찾을 수 없는 경우 404을 응답한다.', (done) => {
      request(app)
          .get('/users/999')
          .expect(404)
          .end(done);
    });
  });
})
```

그 다음 그것을 만족시킬 수 있는 코드를 작성한다.

맨 처음부터 다 만들려고 하지 말고 각 케이스를 통과할 수 있도록 코드를 짜고 테스트해본 다음, 그 다음 케이스를 만족시킬 수 있는 코드를 차례차례 추가하면 된다.

### 단계별 코드 작성
#### `/users`
성공-1) 유저 객체를 담은 배열로 응답한다.
```js
app.get('/users', (req, res) => {
  res.json(users);
});
```

성공-2) 최대 limit 개수만큼 응답한다.
```js
app.get('/users', (req, res) => {
  const limit = req.query.limit;
  res.json(users.slice(0, limit));
});
```

실패-1) limit가 숫자형이 아니면 400을 응답한다.

```js
app.get('/users', (req, res) => {
  const limit = parseInt(req.query.limit, 10);
  if(Number.isNan(limit)) {
    return res.status(400).end();
  }
  res.json(users.slice(0, limit));
});
```
> 그런데 이렇게 작성하면 `성공-1) 유저 객체를 담은 배열로 응답한다.`를 만족시킬 수 없다. `/users`로 GET 요청을 하면 limit가 없기 때문에 parseInt()의 결과값이 undefined이므로 NaN이다. 따라서 status 400을 반환하기 때문에 테스트를 만족할 수 없는 것이다. limit 파라미터가 없는 경우를 대비해 기본값을 할당하는 코드를 추가한다.

```js
app.get('/users', (req, res) => {
  req.query.limit = req.query.limit || 10;
  const limit = parseInt(req.query.limit, 10);
  if(Number.isNan(limit)) {
    return res.status(400).end();
  }
  res.json(users.slice(0, limit));
});
```

#### `/users/:id`
성공-1) id가 1인 유저 객체를 반환한다.
```js
app.get('/users/:id', (req, res) => {
  const id = parseInt(req.params.id, 10);
  const user = users.filter(user => user.id === id)[0];
  res.json(user);
})
```

실패-1) id가 숫자가 아닐 경우 400을 응답한다.
```js
app.get('/users/:id', (req, res) => {
  const id = parseInt(req.params.id, 10);
  if(Number.isNaN(id)) {
    return res.status(400).end();
  }
  const user = users.filter(user => user.id === id)[0];
  res.json(user);
})
```

실패-2) id로 유저를 찾을 수 없을 경우 404를 응답한다.
```js
app.get('/users/:id', (req, res) => {
  const id = parseInt(req.params.id, 10);
  if(Number.isNaN(id)) {
    return res.status(400).end();
  }
  const user = users.filter(user => user.id === id)[0];
  if(!user) {
    return res.status(404).end();
  }
  res.json(user);
})
```

## 2. 삭제
### `DELETE /users/:id`
(1) 성공시
- 204를 응답한다.

(2) 실패시
- id가 숫자가 아닐 경우 400을 응답한다.

### 테스트 코드와 단계별 코드
조회 부분에서 배운 걸 응용하면 된다.  
삭제 요청을 받으면 요청받은 id의 유저가 아닌 유저만 배열에 담아 반환하고 204를 응답하는 식으로 하면 된다.

## 3. 추가
### `POST /users`
(1) 성공시
- 생성된 유저 객체를 반환한다.
- 입력한 name을 반환한다.

(2) 실패시
- name 파라미터 누락시 400을 반환한다.
- name이 중복일 경우 409를 반환한다.

### 테스트 코드
```js
describe('POST /users는', () => {
  describe('성공시', () => {
    let name = 'Daniel',
        body;
    // 두 개의 it에 공통된 로직이 있으므로, 테스트를 실행하기 전에 먼저 실행
    before(done => {
      request(app)
          .post('/users')
          .send({name: name})
          .expect(201)
          .end((err, res) => {
            body = res.body;
            done();
          })
    })
    it('생성된 유저 객체를 반환한다.', () => {
      body.should.have.property('id');
    });
    it('입력한 name을 반환한다.', () => {
      body.should.have.property('name', name);
    });
  });
  describe('실패시', () => {
    it('name 파라미터 누락시 400을 반환한다.', (done) => {
      // ...
    });
    it('name이 중복일 경우 409를 반환한다.', (done) => {
      // ...
    });
  });
});
```

### 단계별 코드 작성
body parser를 사용하기 위해 다음 두 개의 미들웨어를 app.get()보다 위에 추가해준다. `application/json`과 `application/x-www-form-urlencoded`(폼에서 name, value로 전송하는 값)을 body로부터 읽기 위함이다. express 4 이전에는 body-parser 모듈이 따로 필요했는데 지금은 내장 모듈로 해결할 수 있다.
```js
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
```

성공-1) 생성된 유저 객체를 반환한다.
성공-2) 입력한 name을 반환한다.
```js
app.post('/users', (req, res) => {
  const name = req.body.name;
  const id = Date.now();
  const user = {id, name};
  users.push(user);
  res.status(201).json(user);
});
```

실패-1) name 파라미터 누락시 400을 반환한다.
```
if문으로 name 여부를 검사한다.
```


실패-2) name이 중복일 경우 409를 반환한다.
```
users.filter()로 name이 같은 유저의 배열을 구해 length를 구한다. if문으로 length를 검사해 409 응답
```

## 4. 수정
### `PUT /users/:id`
(1) 성공시
- 변경된 name을 응답한다.

(2) 실패시
- 정수가 아닌 id일 경우 400 응답
- name이 없을 경우 400 응답
- 없는 유저일 경우 404 응답
- 이름이 중복될 경우 409 응답

### 테스트 코드
```js
describe('PUT /users/:id는', () => {
  describe('성공시', () => {
    it('변경된 name을 응답한다.', (done) => {
      // res.body를 should.have.property로 검사하는데, name 프로퍼티와 특정 이름을 가졌는지 검사하면 된다.
    });
  });
  describe('실패시', () => {
    it('정수가 아닌 id일 경우 400 응답', (done) => {
      // ...
    });
    it('name이 없을 경우 400 응답', (done) => {
      // ...
      // res.send({})를 해본다.
    it('없는 유저일 경우 404 응답', (done) => {
      // ...
    });
    it('이름이 중복될 경우 409 응답', (done) => {
      // ...
      // 여기서 중복된다는 것은 요청한 id가 아니더라도 다른 사람이 같은 name을 가진 경우를 의미한다.
    });
  });
});
```

### 단계별 코드 작성
성공-1) 변경된 name을 응답한다.
```
// ...
```

실패-1) 정수가 아닌 id일 경우 400 응답
```
req.params.id를 parseInt()한 결과를 Number.isNaN()로 검사
```

실패-2) name이 없을 경우 400 응답
```
req.body.name으로 얻은 name을 검사한다.
```

실패-3) 없는 유저일 경우 404 응답
```
users.filter()로 배열의 [0]번째 내용을 가져와서, if문으로 검사해 404 응답
```

실패-4) 이름이 중복될 경우 409 응답
```
users.filter()로 name과 user의 name이 같은지 검사해 배열을 구한다. if문으로 length를 검사해 409 응답
```