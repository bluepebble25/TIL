# 이벤트 루프, 콜스택, 콜백큐


## 자바스크립트는 싱글 스레드 언어
JS가 싱글 스레드라고 하는 것은 이벤트 루프를 두고 하는 말이다. 이벤트루프는 결과를 가져오는 역할을 하는 스레드로써만 존재하고, 실질적인 일은 Web API가 생성한 스레드들이 처리한다. 이런 일련의 과정에 콜스택, 이벤트 루프, 콜백큐가 관여한다. 자세히 알아보자.

<img src="./img/javascript_engine.png" alt="" width="750">

- Call Stack: 요청받은 함수를 스택에 담아 순차적으로 실행하는 역할. 힙 메모리와의 관계로 설명하면 콜 스택은 변수 이름, primitive 값, 스코프 체인, this, 코드 실행 순서를 저장하고 힙 메모리는 콜 스택의 변수가 참조하고 있는 데이터(Object)를 저장하고 있다.
- Web API: 웹 브라우저에서 제공하는 API로 AJAX나 Timeout등의 비동기 작업을 실행
- Callback Queue: Task Queue라고도 하며 Web API에서 넘겨받은 Callback함수를 저장.
- Event Loop: Call Stack이 비어있다면 Task Queue의 콜백 함수를 Call Stack으로 옮김

## 예제로 엔진의 동작 알아보기
setTimeout의 시간을 0으로 지정하고 아래와 같은 순서로 코드를 실행했다.

```js
function first() {
  console.log("first: timeout 함수");
}
setTimeout(first, 0);
console.log("second");

```
결과)  
> second  
> first: timeout 함수

왜 이런 결과가 나온 것일까?
1. setTimeout() 함수가 call stack에 등록되고 자바스크립트 엔진은 Web API에 작업을 요청한다. 그와 동시에 call stack에서는 해당 작업이 사라진다.
2. `console.log("second")`가 call stack에 들어오고, 실행된다.
3. 이 순간 Web API는 다른 스레드에게 콜백함수(`first()`)와 함께 작업(0초 기다리기)을 위임하고 있다.
4. Web API가 setTimeout()의 실행을 끝내면 callback queue에 callback 함수(`first()`)를 보낸다.
5. 이벤트 루프는 call stack이 완전히 비어있는지 확인하고 callback queue에 있는 callback 함수를 call stack으로 가져온다.
6. call stack이 callback 함수(`first()`)를 실행해 console.log("first")의 결과가 화면에 출력된다.

## Reference
- [자바스크립트는 왜 싱글 쓰레드일까?](https://chanyeong.com/blog/post/44)
- [How JavaScript works: an overview of the engine, the runtime, and the call stack](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf)
- [콜스택/메모리힙 구조, 데이터 저장/참조 원리](https://charming-kyu.tistory.com/19)
- [비동기적 Javascript – 싱글스레드 기반 JS의 비동기 처리 방법](https://hudi.blog/async-javascript/)