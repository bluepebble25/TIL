# http 모듈로 서버 생성하기, 라우팅
> 목차
> 1. [서버 생성하기](#1-서버-생성하기)
> 2. [서버 동작시키고 GET 요청 보내보기](#서버-동작시키고-get-요청-보내보기)
> 3. [라우팅](#라우팅)
> 4. [Express를 사용하는 이유](#express를-사용하는-이유)

## 1. 서버 생성하기
```js
// index.js
const http = require('http');

const hostname = '127.0.0.1';
const port = 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World');
});

server.listen(port, hostname, () => {
  console.log('Server running at http://${hostname}:${port}');
});
```

### 해석
1. `const http = require(http);` - node.js에 내장되어 있는 http 모듈을 불러온다.
2. `http.createServer(...)` 부분 - `http.createServer()`로 서버를 생성하고, 안에있는 함수를 http 요청을 받을 때마다 실행한다. 그 함수는 request 객체와 response 객체를 파라미터로 받는다. 여기서는 응답으로 200이라는 성공을 의미하는 상태코드와 헤더, 메세지를 보내고 있다.
3. `server.listen(...)` - 서버가 클라이언트의 응답을 대기하고 있는 상태로 만든다.

### http.createServer()
```js
const http = require('http');
const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World');
});
```
이것과

```js
const http = require(http);
const server = http.createServer();
server.on('request', (req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World');
});
```
는 같다.  
`server.on('request', (req, res) => {...});`는 request 이벤트를 `server` 객체에 등록한다는 의미이다. 그 외에도 `connection`, `close` 등의 이벤트를 등록할 수도 있다.

`createServer((req, res) => {...})`는 http.createServer()와 `server.on`을 이용한 request 이벤트의 등록을 축약한 것이다.

## 서버 동작시키고 GET 요청 보내보기
### 윈도우 환경
두 개의 cmd 창이 필요하다.
먼저 index.js가 존재하는 폴더로 이동해
```
> node index.js
```
를 입력해 서버를 실행한다. 다른 cmd 창에서
```
curl localhost:3000
```
을 입력하면 GET 요청을 보낸 것이다. 응답이 `Hello World`라고 오는 걸 볼 수 있다.

curl은 cURL을 의미하는데, 여러 통신 프로토콜을 이용해 데이터를 전송할 수 있게 해주는 라이브러리라고 생각하면 된다. 윈도우에서 기본적으로 사용할 수 있다.

### 리눅스 환경
리눅스에도 기본적으로 탑재되어 있다. -X 옵션으로 request 메소드를 지정할 수 있다.
```
curl -X GET "localhost:3000"
```

## 라우팅
### URI 구분하기
위의 `index.js`예제에서
```
curl localhost:3000
curl localhost:3000/
curl localhost:3000/users
```
이렇게 어떤 경로로 요청을 하든 모두 루트(/)경로에 요청을 한 것과 같은 결과(Hello World)를 얻는다. 이는 라우팅을 해주지 않았기 때문이다.

라우팅이란 클라이언트가 보낸 요청과 경로(URI)에 해당되는 응답을 해주는 것이다.  
그러려면 먼저 클라이언트가 보낸 요청의 경로를 알아야 한다. `req.url`과 같이 request 객체의 url 부분을 확인함으로써 라우팅을 해줄 수 있다.
```js
const http = require('http');

const hostname = '127.0.0.1';
const port = 3000;

const users = [
  {id: 1, name: 'Tom'},
  {id: 2, name: 'Marie'},
];

const server = http.createServer((req, res) => {
  if(req.url === '/') {
    res.statusCode = 200;
    res.setHeader('Content-Type', 'text/plain');
    res.end('Hello World');
  } else if(req.url === '/users') {
    res.statusCode = 200;
    res.setHeader('Content-Type', 'application/json');
    res.end(JSON.stringify(users));
  } else {
    res.status = 404;
    res.end('Not Found');
  }
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}`);
});
```

주의하자! JSON.stringfy가 아니라 string'i'fy다!

이렇게 사용자가 요청한 URI에 맞는 내용을 보내줄 수 있게 되었다. 지금 만든게 바로 사용자 목록 조회 API이다!

### GET, POST 요청 구분
req.url로 클라이언트가 원하는 자원을 알아내는 것 뿐만 아니라 req.headers, req.method를 통해서 헤더 정보와 GET 요청인지, POST 요청인지 확인해 그에 맞는 동작을 할 수 있다.

```js
http.createServer((req, res) => {
  if(req.method === 'POST') {
    //...
  }
  if(req.method === 'GET') {
    //...
  }
})
```
처럼 응용할 수 있다. 그러면 이제 GET과 POST 요청에 응답할 수 있는 API를 만든 것이다!

## Express를 사용하는 이유
GET, POST, PUT, DELETE 그리고 수많은 URI까지... 모든 경우의 수에 맞춰 만들려고 하다보니 계속 if문이 줄줄이 이어져 코드를 작성과 유지보수가 힘들고, 상태코드 설정과 헤더 설정 등 중복되는 코드가 많이 발생한다. 그래서 나온 게 Node.js 프레임워크, Express다. Express는 API의 사용법이 단순화되고 유용한 기능이 추가되었고, 라우팅을 하기 쉽다. 기존 node.js 객체를 래핑해서 만들었기 때문에 동작은 똑같이 하지만 더 간단하게 사용할 수 있고, 유용한 기능이 더해졌다.