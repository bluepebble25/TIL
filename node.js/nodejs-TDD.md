# Node.js TDD (Mocha + Should + SuperTest 조합)

- [TDD란?](#tdd란)
- [Mocha](#mocha)
- [Should](#should)
- [SuperTest](#supertest)

## TDD란?
테스트 주도 개발(Test-Driven-Development)의 약자이다.
- 설계 -> 코드 개발 -> 테스트 🙅‍♀️
- 설계 -> 테스트 코드 작성 -> 코드 개발 🙆‍♀️

책을 쓸 때 목차부터 쓰고 세부 내용을 채워가는 것처럼, 먼저 작은 단위의 테스트 케이스를 작성하고 그 것을 통과하는 코드를 만드는 것을 반복한다.

이 글에서는 단위 테스트, 통합 테스트를 세 가지 라이브러리를 이용해 하는 방법을 알아볼 것이다.

각 라이브러리의 역할은 다음과 같다.
- Mocha - 테스트 코드를 돌려주는 테스트 러너.
- Should - 값 검증을 위한 써드파티 라이브러리로, 노드의 기본 모듈인 assert와 같은 기능을 한다. 가독성을 위해 assert 대신 사용된다.
- SuperTest - 통합 테스트를 위한 라이브러리. 통합 테스트란 API의 기능을 테스트 하는 것으로, 여기서는 SuperTest가 익스프레스 서버를 구동해 요청을 보내고 결과를 검증하는 방식으로 이뤄진다.

## Mocha
### 1. 설치
- 개발 의존성 모듈로 추가하기 (node_modules에 설치되며 `package.json`의 `devDependencies`에 추가됨)
  ```
  npm install mocha --save-dev
  ```
- 전역으로 설치하기
  ```
  npm install mocha -g
  ```
### 2. 테스트 코드 파일 만들기
예를 들어 `calculator.js`를 테스트한다고 하면 그것의 테스트 코드는 `calculator.spec.js`라는 이름으로 만든다.

### 3. 테스트 코드 작성
- 테스트 슈트(test suite)
  - 테스트 케이스들이 모여서 하나의 테스트 슈트를 이룬다.
  - mocha에서는 `describe()`에 해당
- 테스트 케이스(test case)
  - 가장 작은 테스트의 단위
  - mocha에서는 `it()`에 해당

```js
// index.spec.js
const pow = require('./index');
const assert = require('assert');

describe('pow 함수는', () => {
  it('주어진 숫자의 n 제곱을 반환한다.', () => {
    assert.equal(pow(2, 3), 8);
  });
  it('3을 4번 곱하면 81이다.', () => {
    assert.equal(pow(3, 4), 81);
  });
  it('5를 3번 곱하면 125이다.', () => {
    assert.equal(pow(5, 3), 125);
  });
});
```

```js
// index.js
module.exports = function (a, b) {
  return Math.pow(a, b);
};
```

여기서는 pow 함수라는 간단한 예시를 들었기 때문에 it()을 여러 개 사용하려는 예시를 위해 저렇게 썼지만, 보통은 어떤 함수가 하는 행위를 잘게 쪼개거나 발생하는 여러 경우의 수를 테스트하는 방식으로 작성한다.

예를 들면, json 데이터를 불러와 그 중 한 필드의 값을 구하는 함수를 테스트 한다면, describe에는 그 함수의 이름을 쓰고, it에는 각각 비동기로 데이터를 불러오는 행위를 성공하는지, 받은 데이터가 json 형식인지, 특정 필드의 값을 구할 때 결과가 올바른지 처럼 작성할 수 있다.

### 4. 실행방법
- 윈도우
  ```
  node ./node_modules/mocha/bin/mocha index.spec.js
  ```
- 리눅스
  ```
  node_modules/.bin/mocha index.spec.js
  ```

결과
> pow 함수는  
√ 주어진 숫자의 n 제곱을 반환한다.  
√ 3을 4번 곱하면 81이다.  
√ 5를 3번 곱하면 125이다.  
3 passing (9ms)

## Should
노드 모듈에서 기본적으로 제공하는 assert 모듈을 확장한 써드파티 라이브러리이다. `A.should.be.equal(B)`, `A.should.not.be.equal(B)` 처럼 보다 가독성있게 값을 검증할 수 있다.

### 1. 설치
```
npm install should --save-dev
```

### 2. 사용 예
```js
// utils.spec.js
const utils = require('./utils');
const should = require('should');

describe('utils 모듈의 capitalize 함수는', () => {
  it('문자열의 첫번째 문자를 대문자화한다.', () => {
    const result = utils.capitalize('Hello');
    result.should.be.equal('Hello');
  });
});
```

```js
// utils
function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

module.exports = {
  capitalize: capitalize
};
```

여기서는 간단한 사용 예시만. 자세한 함수는 검색으로 알아보기

## SuperTest
```js
// index.js
const express = require('express');
const app = express();

const users = [
  {id: 1, name: 'James'},
  {id: 2, name: 'Amelia'},
  {id: 3, name: 'Luna'}
];

app.get('/users', (req, res) => {
  res.json(users);
});

app.listen(3000, () => {
  console.log('Server is running on port 3000');
});

module.exports = app;
```

```js
// index.spec.js
const app = require('./index');
const request = require('supertest');

describe('GET /users는', () => {
  it('json 배열을 반환받는다.', (done) => {
    request(app)
        .get('/users')
        .expect('Content-Type', /json/)
        .expect(200, done);
  });
});
```
mocha로 실행해준다.

supertest를 request라는 이름으로 불러오고, `request(app)`을 통해 supertest가 express 서버를 실행하도록 한다. 체이닝 메소드 형식으로 supertest가 어플리케이션에게 보내는 요청과 기대하는 값을 시나리오처럼 작성하면 된다. done은 비동기처리를 해주기 위한 콜백함수이다.

만약 API 호출의 결과값을 검증해야 한다면 여기에도 마찬가지로 Should를 쓸 수 있을 것이다.

이로써 Mocha + Should + SuperTest를 이용해 단위 테스트와 통합 테스트를 하는 방법을 알아보았다.

## Reference
- http://clipsoft.co.kr/wp/blog/tddtest-driven-development-%EB%B0%A9%EB%B2%95%EB%A1%A0/
- https://hanamon.kr/tdd%EB%9E%80-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%A3%BC%EB%8F%84-%EA%B0%9C%EB%B0%9C/
- https://www.npmjs.com/package/supertest