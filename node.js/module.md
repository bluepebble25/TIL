# node.js 모듈
모듈이란 독립적인 기능을 가진 단위이다. 파일 하나하나가 모듈이 될 수 있다. 모듈을 사용하면 비슷한 의미를 갖는 코드를 파일마다 분리해서 관리할 수 있고, 프로그램을 확장하기에도 좋다. 그리고 파일마다 독립된 스코프를 갖기 때문에 같은 이름을 가진 변수가 존재해도 충돌하지 않는다.

모듈을 내보내는 것은 exports 키워드, 불러오는 것은 require()로 할 수 있다.
### 예제 1
```js
// foo.js
const a = 10;
exports.a = a;
```

```js
// bar.js
const a = 5;
const foo = require('./foo.js');
console.log(foo.a);
```
> \> `node foo.js`  
> \> 10

### 예제 2
```js
// calculator.js
exports.add = function(a, b) {
  return a + b;
}

exports.sub = function(a, b) {
  return a - b;
}
```

```js
// index.js
const cal = require('./calculator');
console.log(cal.add(3, 5));
console.log(cal.sub(10, 3));
```
> \> `node index.js`  
> 8  
> 7  

### 예제 3 (module.exports)
위의 calculator.js는 이렇게 작성할 수도 있다.
```js
// calculator.js
function add(a, b) {
  return a + b;
}
function sub(a, b) {
  return a - b;
}
module.exports = {
  add: add,  // add라는 이름으로 add라는 함수를 내보낸다
  sub: sub,
}

// 또는
// module.exports.add = function(a, b) {...};
```

객체 한 개만 내보낼 때는 module.exports로 바로 내보낼 수 있다.
```js
// hello.js
function hello() {
  console.log('hello');
}
module.exports = hello;
```

### 원리
모듈(파일) 간의 정보 교환은 exports라는 객체를 통해 이루어진다. exports는 module.exports의 축약형으로 module.exports를 참조하고 있다.

만약
```js
module.add = function(a, b) {
  return a + b;
};
module.num = 10;
```
을 작성하면

```js
module.exports = {
  add: function(a, b) {
    return a + b;
  },
  num: 10
};
```
라는 객체를 만든 것과 마찬가지다. `require('파일명')`을 하면 그 파일의 `module.exports` 객체를 불러오는 것이다. 덕분에 파일마다 고유의 스코프를 유지할 수 있다.