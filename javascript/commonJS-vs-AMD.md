# CommonJS VS AMD 그리고 ES6 모듈
모듈(Module)은 기능별로 분리되어 작성된 파일이다. 대부분의 언어에서는 import로 모듈을 불러와 사용할 수 있다.

js에서는 모듈 시스템을 제공하지 않아 \<script>로 js 파일을 불러오는 방식을 사용했다. 하지만 이 방법은 완벽한 모듈화는 아니다. 파일마다 독립된 스코프를 갖지 않고 하나의 전역 객체(Global Object 또는 Window Object)를 공유하기 때문에, 중복된 이름의 변수가 있다면 충돌이 일어난다.
따라서 모듈 시스템을 만들기 위해 모인 그룹이 생겨났는데 그게 바로 CommonJS 그룹과 AMD 그룹이다. 해당 조직에서 제안한 모듈 시스템의 방법들을 CommonJS, AMD라고 부른다.

ES6에서부터 제공하는 표준 모듈 시스템은 CommonJS, AMD 방식의 장점만을 섞어 만든 것이다. 그럼 각각 어떤 방식으로 모듈 시스템을 구현했는지 알아보자.

## CommonJS
CommonJS는 'Common'에서 알 수 있듯이 브라우저 외의 환경(서버, 데스크톱 애플리케이션)에서도 사용할 수 있는 '범용적인' 모듈 시스템을 목표로 한다.

### node.js
모듈(파일) 간의 정보 교환은 exports라는 전역 객체를 통해 이루어진다.require()로 모듈을 불러올 수 있다.
```js
// foo.js
const a = 10;
exports.a = a;

/* 함수를 export 하고 싶다면
exports.sum = function(a, b) { return a + b; }; */
```

```js
// bar.js
const a = 5;
const foo = require('./foo.js');
console.log(foo.a);
```

`node bar.js`로 실행해보면 10이 나온다.

이런 파일 시스템의 구현이 가능한 이유는 다음과 같다.  
`foo.js`에서 `module.a = a`라는 코드를 작성하면
```js
module.exports = {
  a = 10;
}
```
라는 객체를 만든 것과 마찬가지다. 이제 `bar.js`에서 `require('./foo.js')`를 하게 되면 `bar.js`의 module.exports 객체를 가져온다. `exports`객체는 이제 그 객체를 할당한 foo라는 변수를 통해 접근할 수 있다. - `foo.a`.  
이렇게 exports를 통해 데이터를 공유하고 파일마다의 스코프를 지킬 수 있는 것이다.

참고 > exports는 module.exports의 축약형이라고 생각하면 된다. 여러 개를 내보낼 때는 `exports.a`를, 단일 객체를 내보낼 때에는 `module.exports = a`처럼 사용한다.

### 문제점
하지만 범용이라는게 '서버 사이드 js 환경에서도 가능하다'라는 뜻이기 때문에 애플리케이션의 의존 모듈들이 모두 로컬 디스크에 존재한다는 것을 전제로 한다. 따라서 동기적(synchronously)으로 모듈을 호출하는 방식을 선택했다. 필요한 모듈을 모두 다운받을 때까지 아무것도 할 수 없기 때문에 이는 브라우저 환경에서 사용하기에 적합하지 않다.

## AMD(Asynchronous Module Definition)
js 모듈 사용에 대해 CommonJS와 합의점을 찾지 못하고 독립한 그룹이다. AMD는 브라우저에서의 모듈 실행(비동기적으로 모듈 다운)을 우선적으로 여겼기 때문이다.

모듈 정의 - define()
```js
define(['의존모듈1', '의존모듈2'],
  // 의존모듈 로딩 끝나면 실행할 함수
  function('의존모듈1 담을 파라미터', '의존모듈2 담을 파라미터'){
    // 코드
    function 함수1 {};
    return {
      // 외부에 노출할 함수만 반환
      함수1 : 함수1 // 함수1 이라는 이름으로 함수1을 외부에 노출
    };
  });
```

모듈 사용 - require()
```js
require([
 ...
 // 사용할 모듈 배열로 나열
], function (...) {
 // 사용할 모듈들이 순서대로 매개변수에 담김
 // 불러온 모듈을 사용하는 코드
});
```
문법이 조금 복잡하긴 하지만 비동기적으로 모듈을 다운로드 하기 때문에 CommonJS보다는 퍼포먼스가 좋다. 또한 브라우저와 서버 사이드 모두에서 호환된다.

## ES6 Module
아무리 저렇게 온갖 난리를 쳐봐도 언어 자체에서 제공하는 것만 못하다. ES6부터는 언어 자체에서 표준 모듈 시스템을 정의했다. 장점은 다음과 같다.
- 동기/비동기 로드를 모두 지원
- 문법이 간단. import, export 키워드만으로 사용 가능
- CommonJS와는 다르게 실제 객체/함수를 바인딩하기 때문에 순환 참조 관리도 편하다.
- 정적 분석(static analyze, 코드를 실행하지 않더라도 분석이 가능함)이 가능하기 때문에, 트리 쉐이킹 역시 쉽게 가능하다.

ES6 모듈의 파일 확장자는 모듈 파일임을 명확하게 하기 위해 `.mjs`를 사용하도록 권장하고 있다.
script 태그에 type="module" 어트리뷰트를 추가하면 로드된 자바스크립트 파일은 모듈로서 동작한다.
```html
<script type="module" src="lib.mjs"></script>
```

### **복수 개체** 불러오기/내보내기
js 파일 안에서의 사용은 다음과 같다.
```js
// 불러오기
import {sum} from './calculator.js'
import {sum, sub} from './calculator.js'
import {sum} as add from './calculator.js'
import * from './foo.js'
```

```js
// 내보내기
export function sum(a, b) {
  return a + b;
};

const sub = function(a, b) {
  return a - b;
}
export {sub};
```
내보낼 때에는 대상 객체 앞에 `export` 키워드를 붙여도 되고 `export {객체}`처럼 해도 된다.

### **단일 객체** 불러오기/내보내기
```js
// 불러오기
import sum from './calculator';
```

```js
// 내보내기
function sub(a, b) {  // 안내보냄
  return a + b;
}

export default sum(a, b) {
  return a + b;
}

// or function sum(a, b){} 작성해놓고 파일 마지막에 export default로 내보내기
export default sum;
```

## 마무리
이 문서에서는 CommonJS가 뭔지, AMD가 뭔지에 대한 기본 개념과 흐름을 살펴보았다. 이 두 개를 이해한다면 모듈 라이브러리나 모듈 번들러(ex-Webpack) 같은 것들이 어떤 문제를 해결하기 위해 나왔는지 알고 잘 사용할 수 있을 것이다.

## Reference
- https://poiemaweb.com/es6-module
- [JavaScript 번들러로 본 조선시대 붕당의 이해](https://yozm.wishket.com/magazine/detail/1261/)
- https://d2.naver.com/helloworld/12864
- [require(), exports, module.exports 공식문서로 이해하기](https://medium.com/@chullino/require-exports-module-exports-%EA%B3%B5%EC%8B%9D%EB%AC%B8%EC%84%9C%EB%A1%9C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-1d024ec5aca3)