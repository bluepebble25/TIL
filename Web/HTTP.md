# HTTP

> 목차  
> [1. HTTP 특징](#1-http-특징)  
> [2. HTTP 메시지 구조](#2-http-메시지-구조)  
> [3. HTTP 메소드](#3-http-메소드)  
> &nbsp;&nbsp;&nbsp;&nbsp;[http 메소드와 멱등성(idempotent)](#http-메소드와-멱등성idempotent)  
> [4. 자주 사용되는 상태 코드](#4-자주-사용되는-상태-코드)

## 1. HTTP 특징
1. 비연결성(Connectionless) - 클라이언트와 서버가 요청과 응답을 할 때마다 매번 연결을 새로 맺었다 끊는 것을 의미한다.
2. 무상태성(Stateless) - 비연결성으로 인해 생긴 특징. 매 번 새로 연결을 맺기 때문에 서버가 상태를 기억하지 않는다. 따라서 상태를 기억하기 위한 보조수단으로 쿠키나 세션을 이용한다.

## 2. HTTP 메시지 구조

### Request인 경우

|시작 줄|
|-------|
|Header|
|공백|
|Body(경우에 따라 존재)|

- 시작 줄(start line): HTTP 메소드 / URL / HTTP 프로토콜 버전  
  <img src="https://mdn.mozillademos.org/files/13687/HTTP_Request.png" alt="http 요청라인과 헤더 예제" width="500">

  <img src="./img/HTTP_Request_Headers.png" alt="http request 메시지 예제" width="600">

  POST(새로운 리소스 create) 요청을 하고, 요청하는 대상의 URL은 /(루트), HTTP 버전은 1.1이란 의미

### Response인 경우

|상태 줄|
|-------|
|Header|
|공백|
|Body|

- 상태 줄(status line): HTTP 프로토콜 버전 / 상태코드(ex- 200) / 상태 텍스트 (ex- OK)  
<img src="./img/HTTP_Response_Headers.png" alt="http response 메시지 예제" width="500">

### 공통 부분
- Header: 클라이언트와 서버가 요청 또는 응답으로 부가적인 정보를 전송할 수 있도록 해준다.  
- Body: POST/PUT 요청을 하며 보내야 하는 정보가 있을 때, 혹은 request에 대한 응답을 받을 때 Body 부분에 정보가 담겨 전송된다.

## 3. HTTP 메소드
| 메소드 | 설명 |
|--------|-----|
|GET|서버에게 리소스를 **조회**해달라고 요청한다. 쿼리 스트링(?id=123&name=coffee)의 형태로 파라미터를 전달할 수 있다. 파라미터에 내용이 노출되기 때문에 민감한 정보를 다룰 때에는 사용하지 말자.|
|POST|URI에 해당하는 리소스를 **생성**해달라고 요청한다. 데이터는 Body에 담겨 전송된다. 민감한 정보가 파라미터가 아닌 Body에 담기기 때문에 GET보다 안전하다고 생각할 수 있지만 개발자 도구 같은 툴로 보면 다 보이므로 민감한 내용이라면 암호화하자.|
|PUT|URI에 해당하는 리소스를 **수정**해달라고 요청한다. 데이터는 Body에 담겨 전송된다.|
|DELETE|해당 리소스를 **삭제**해달라고 요청한다.|
|HEAD|GET과 비슷하지만, 응답으로 헤더와 BODY가 아닌 **헤더만 받는다.** 헤더만 받기 때문에 리소스를 가져올 필요 없이 정보를 얻을 수 있다.|
|PATCH|PUT과 비슷하게 리소스를 **수정(UPDATE)** 할 때 사용하지만, PUT은 전체를 업데이트하는 반면, PATCH는 **일부만을 변경**한다.|
|CONNECT|요청한 리소스에 대해 양방향 연결을 요청한다. 클라이언트가 CONNECT를 요청하면 서버가 클라이언트와 목적 URI 사이를 연결해준다. 서버가 **프록시**의 역할을 하게 되는 것이다. 주소 세탁을 위해 프록시로 사용되는 등의 악성 공격에 노출될 수 있으므로 주의해야 한다. 서버의 설정에서 이 메소드를 비활성화 시켜놓자.|
|OPTIONS|웹 서비스에서 지원되는 메소드의 종류를 확인할 경우 사용한다. OPTIONS 요청을 하면 서버가 응답을 하는데, 헤더 부분의 Allow에 `Allow: GET, POST, HEAD`와 같이 허용된 메소드가 나열되어 있다.|
|TRACE|서버에게 메시지를 보내고 다시 반사받을 수 있다. 예를 들어 `TRACE / HTTP/1.1` 요청과 함께 헤더를 보내면 다시 그 메시지를 돌려받는다. 하지만 서버가 반사하는 메시지를 가로채면 헤더에 포함되어 있는 클라이언트의 쿠키를 가로챌 수 있으므로 이 메소드도 비활성화 시키자.|


### HTTP 메소드와 멱등성(idempotent)
#### idempotent(멱등성)이란  
여러 번 연산을 해도 같은 결과를 얻는 성질. GET 요청을 여러 번 해도 같은 결과를 얻으므로 GET은 멱등하고(idempotent), POST는 결과가 달라지므로 멱등하지 않다(non-idempotent).

#### POST와 PUT의 차이
POST는 리소스의 위치를 지정하지 않아도 서버에서 자동으로 리소스를 생성해준다.
```
// request
POST /students HTTP/1.1
{
  name: 김땡땡,
  grade: 1
}

// 앞으로 이 문서에 나오는 모든 body의 json 데이터는 stringfy 여부를 신경쓰지 말기로 하자. 원래는 stringfy된 형태여야 옳다. ("name": "김땡땡", "grade": "1")
```
id를 지정해주지 않아도 서버에서 자동으로 /students/1로 리소스를 생성한다. 여러 번 POST를 요청하면 /students/2, /students/3로 중복된 내용을 갖는 여러 학생 정보가 생성된다. 하지만 이를 두고 동일한 결과가 발생했다고 하지는 않는다. 따라서 POST는 멱등하지 않다.

PUT은 요청을 보낼 때 리소스의 위치를 자세히 지정해줘야 한다. POST는 `/students`만으로도 서버에서 알아서 하위에 리소스를 생성해주었지만 PUT은 `/students/1`처럼 자세히 지정해줘야 해당 리소스를 수정할 수 있다. 만약 `/students/1`이 존재하지 않는 리소스라면 그 리소스를 생성해주고 이후의 같은 요청에서는 계속 `/students/1`을 수정해 같은 결과를 낳으므로 PUT은 멱등하다고 할 수 있다.

/students/1에서 students는 Collection URI, 1은 Resource Identifier라고 부른다.

#### PUT과 PATCH의 차이
PUT은 해당 자원의 전체를 교체하는 반면, PATCH는 일부만을 변경한다.
그리고 PUT은 멱등하지만(여러 번 같은 요청을 해도 같은 결과를 얻는다), PATCH는 멱등하지 않다. 왜 PUT은 멱등하고 PATCH는 멱등하지 않다는 것일까? 사례를 통해 알아보자.

```
// 기존 리소스
{
  id: 1
  name: "Happy",
  age: 21
}
```

1. PUT 요청
    ```
    PUT users/1 HTTP/1.1

    {
      age: 22
    }
    ```

    리소스 변경 결과  
    ```
    {
      id: 1
      name: null
      age: 22
    }
    ```
    PUT은 전체를 새 데이터로 교체하는 개념이므로 작성하지 않은 필드에 대해서는 데이터가 null이 된다. (API 구현하기 나름이긴 하다.) 어쨌거나 여러 번 요청해도 같은 결과를 얻는다.

2. PATCH
    ```
    PUT users/1 HTTP/1.1

    {
      $increase: "age", value: 1
    }
    ```
    설계는 자유이므로 이렇게 설계할 수도 있다.
    ```
    PUT users/1 HTTP/1.1

    {
      age: {
        operation: $increase,
        value: 1
      }
    }
    ```

    리소스 변경 결과.
    ```
    {
      id: 1
      name: "Happy",
      age: 22
    }
    ```

    작성한 필드 부분만 1 증가된 값으로 교체되고 나머지는 그대로이다. 만약 `age: 22`처럼 명확하게 명시했다면 여러번 요청해도 결과가 같아 멱등성을 가졌을 것이다. 하지만 서버에서 PATCH에 대한 동작을 구현할 때 $increase와 같이 숫자를 1 증가시키는 것과 같은 행동을 하도록 했다면 여러번 실행할 때마다 결과가 달라질 것이므로 멱등하지 않다.  
    따라서 PATCH는 구현에 따라 멱등할 수도 있고 아닐수도 있다는 것이다. 이러한 불확실성 때문에 PATCH는 보통 멱등하지 않다고 말한다.

## 4. 자주 사용되는 상태 코드
| 상태코드 | 메시지 | 설명 |
|---------|--------|------|
|1xx| Informational | 정보 교환|
|2xx| Success | 데이터 전송이 성공적으로 이루어졌거나 수락되었음 (클라이언트의 요청을 처리하는데만 성공한거지, 데이터의 일부만 전송하거나 전송할 데이터가 없는 경우도 있음) |
|3xx| Redirection | 자료의 위치가 바뀌었음 |
|4xx| Client Error | 클라이언트 측의 오류. 주소를 잘못 입력했거나 요청이 잘못되었음|
|5xx| Server Error | 서버측의 에러로 올바른 요청을 처리할 수 없음 |

### 2xx
| 상태코드 | 메시지 | 설명 |
|---------|--------|------|
| 200 | OK | 요청이 성공적으로 수행되었음. 주로 GET에 대한 응답 |
| 201 | Created | 요청에 성공해 새로운 리소스가 생성됨. 주로 POST에 대한 응답 |
| 202 | Accepted | 요청이 접수되었으나 아직 처리는 완료되지 않음. 데이터를 접수해놓고 일정 시간 후에 처리하는 배치 처리와 같은 경우에 대한 응답으로 사용됨 (ex-1시간 뒤에 게시글 삭제 예약)
| 204 | No Content | 요청이 성공적으로 수행되었으나, 응답의 본문에 보낼 데이터가 없을 때. (ex- 삭제 요청이 성공적으로 처리됨 / Save 요청이 성공적으로 처리됨)

### 3xx
서버로부터 3xx 응답을 받으면 Location 헤더에 해당하는 URL로 다시 요청을 보낸다.
```
HTTP/1.1 301 Moved permanently
Location: /new-event
```
자동으로 /new-event 페이지로 이동한다

리다이렉션의 종류
- 영구 리다이렉션: 요청한 URL의 리소스가 다른 곳으로 이동해서 그 곳으로 리다이렉션해줄 때
- 일시적 리다이렉션: 일시적인 변경. 실무에서 많이 사용된다.  
  쇼핑몰에서 결제가 완료되면 다른 화면으로 이동시킬 때 많이 사용됨. 리다이렉션 시키는 이유는, 결제가 완료되었을 때 그 화면에 계속 머물게 한다면, 사용자가 새로고침했을 때 다시 결제 요청이 가게 되어 중복결제가 발생할 수 있기 때문이다.

300~307까지 있음. 자세한 것은 [위키 참조](https://ko.wikipedia.org/wiki/HTTP#%EC%9D%91%EB%8B%B5_%EC%BD%94%EB%93%9C)

### 4xx
| 상태코드 | 메시지 | 설명 |
|---------|--------|------|
| 400 | Bad Request | 클라이언트가 올바르지 못한 요청을 보내 서버가 요청을 이해할 수 없음 |
| 401 | | 클라이언트가 해당 리소스에 대한 인증이 필요함. 예를들어 로그인이 필요한 환경에 비로그인으로 접근했을 경우. 응답에 WWW-Authenticate 헤더와 함께 인증 방법을 설명해서 보낼 수 있다. |
| 403 | Forbidden | 요청은 이해했으나 접근 권한이 부족함 |
| 404 | Not Found | 요청한 리소스가 존재하지 않음 |
| 409 | Conflict | 현재 서버의 상태와 충돌했을 때 (이미 서버에 존재하는 아이디, 닉네임과 충돌한 경우)

### 5xx
| 상태코드 | 메시지 | 설명 |
|---------|--------|------|
| 500 | Internal Server Error | 웹 서버에 문제가 발생했을 때 반환하는 에러. 에러의 원인을 정확하게 파악하지 못한 경우 500으로 처리한다. |
| 503 | Service Unavailable | 서버가 요청을 처리할 준비가 되지 않았을 때. 주로 서버 점검/보수로 인해 일시적으로 작동을 중지할 때 사용한다. |

## Reference
- [HTTP 응답코드 메소드 정리 GET, POST, PUT, PATCH, DELETE, TRACE, OPTIONS](https://javaplant.tistory.com/18)
- [TRACE와 XST(Cross-site Tracing) 공격](https://webhack.dynu.net/?idx=20161111.001)
- [취약한 HTTP 메소드 설정방법(feat. 서버별 설정방법)](https://mokpo.tistory.com/202)
- [HTTP 메소드 PUT , PATCH 차이 - 삽질중인 개발자](https://programmer93.tistory.com/39)
- [HTTP Method의 멱등성](https://velog.io/@gidskql6671/HTTP-Method%EC%9D%98-%EB%A9%B1%EB%93%B1%EC%84%B1)
- [[ RESTful API] PUT과 PATCH의 차이 - 멱동성을 보장하는 PUT, 멱등성을 보장하지 않는 PATCH](https://oen-blog.tistory.com/211)
- [RESTful API POST와 PUT의 차이](https://kingjakeu.github.io/study/2020/07/15/http-post-put/)
- [인프런|모든 개발자를 위한 HTTP 웹 기본 지식|'Patch 메서드가 멱등이 아닌 이유'에 대한 답변](https://www.inflearn.com/questions/110644)
- [자주 사용되는 Http status code를 알아보자.](https://1-7171771.tistory.com/129)