# HTTP 헤더
- HTTP 헤더 - 클라이언트와 서버가 요청 또는 응답으로 부가적인 정보를 전송할 수 있도록 해준다.
- 헤더는 대소문자를 구분하지 않는 이름과 콜론`:` 다음에 줄바꿈없이 이어지는 값으로 이루어져 있다.

## 헤더 종류
헤더는 어떤 역할을 하느냐에 따라 그룹 지을 수 있다.
- General header: response와 request의 헤더 모두에 나타날 수 있지만 바디에서 최종적으로 전송되는 데이터와는 관련이 없는 헤더.
  - `Date, Cache-Control, Connection, ...`
- Entity header: 컨텐츠 길이나 MIME 타입(content type)과 같이 엔티티 바디에 대한 자세한 정보를 포함하는 헤더.
  - `Content-type, Content-length, ...`
- Request header: fetch될 리소스나 request를 요청하는 클라이언트에 대한 자세한 정보를 포함하는 헤더.
  - `Cookie, Referer, Origin, ...`
- Response header: 위치 또는 서버에 대한 정보(이름, 버전 등)와 같이 response에 대한 부가적인 정보를 갖는 헤더.
  - `Server, Location, Access-Control-Allow-Origin, Allow, Age, Expires, ...`


request와 response는 모두 General header와 Entity header를 가질 수 있으며, request는 Request header를, response는 Response 헤더를 갖고 있다.

이제 꼭 알아야 하고 자주 사용되는 헤더들만 자세히 알아보자.

## 1. General header
- **Date**  
  - HTTP 메시지를 생성한 일시 (RFC 1123에서 규정)  
    `Date: Tue, 17 May 2022 06:40:55 GMT`
- **Connection**  
  - 클라이언트와 서버 간 연결에 대한 옵션 설정(다소 모호한 복잡성 있음)
  - Connection: close  
    현재 HTTP 메시지 직후에 TCP 접속을 끊는다는 것을 알린다.
  - Connection: Keep-Alive  
    현재 TCP 커넥션을 유지한다.
- **Cache-Control**
  - 캐시 관련 설정을 할 수 있음. 아래 그림을 참조해 현재 상황에 필요한 설정을 선택하자. (더 많은 설정이 있지만 이것들이 가장 자주 사용되는 설정들이다.)  
  ![](https://web-dev.imgix.net/image/admin/htXr84PI8YR0lhgLPiqZ.png?auto=format&w=650)  
  (그림 출처: https://web.dev/http-cache)
  
  |옵션|설명|
  |---|----|
  | no-store | 아무것도 캐싱하지 않기 |
  | no-cache | 캐시는 저장하지만 사용하려고 할 때마다 서버에 재검증 요청을 보내야 함. max-age=0과 같다 |
  | max-age=\<seconds> | 캐시가 유효한 시간을 지정한다. 캐시가 유효한 기간동안은 서버에 요청을 보내지 않고 디스크 또는 메모리에 저장된 리소스를 이용한다. 만약 캐시가 만료되었다면 저장된 리소스를 삭제하는 것이 아니라 서버에 캐시가 유효한지 재검증 요청을 한 뒤, 저장된 리소스를 사용한다. |
  - Cache에 대한 자세한 설명을 읽고싶다면 이 글을 참고하자. [toss.tech | 웹 서비스 캐시 똑똑하게 다루기](https://toss.tech/article/smart-web-service-cache)

## 2. Entity header
- **Content-Type**
  - 리소스의 미디어 타입 정보
  - 타입 및 서브타입(type/subtype)으로 구성된다.
  - type 종류: application, audio, font, example, image, message, model, multipart, text, video (자세한 건 [MIME type](https://developer.mozilla.org/ko/docs/Glossary/MIME_type) 참조)
  - ex) `Content-Type: text/html; charset=utf-8`
- **Content-Length**
  - 전달되는 해당 개체의 Byte 크기
- **Content-Disposition**
  - 응답 Body를 브라우저가 어떻게 표시해야 할 지 알려준다.
  - inline(웹 페이지 화면에 표시) / attachment(다운로드)
  - `Content-Disposition: attachment; filename='filename.csv'`
  - 다운로드 되길 원하는 파일은 attachment로 값을 설정하고 filename 옵션으로 파일명까지 지정해줄 수 있다. 파일용 서버인 경우 이런 형태가 많이 사용된다.
- **Content-Security-Policy**
  - 다른 외부 파일을 불러오는 경우 차단할 소스와 불러올 소스를 명시한다.
  - XSS 공격을 방지하고 싶다면 신경써야 한다.
  - `Content-Security-Policy: default-src 'none'`  
    외부 파일은 가져올 수 없음
  - `Content-Security-Policy: default-src https:`  
    https를 통해서만 파일을 가져온다.
  - `Content-Security-Policy: default-src 'self'`  
    자신의 도메인의 파일들만 가져온다.
  - `Content-Security-Policy: script-src https://test.com; style-src https://test.com; img-src https://image.test.com;`  
    script 파일과 style 파일, img를 지정된 사이트에서만 가져오도록 허용한다.

## 3. Request 헤더
- **HOST**
  - 요청하려는 서버(호스트)의 도메인명 및 포트번호
- **User-Agent**
  - 클라이언트 소프트웨어(브라우저, OS) 명칭 및 버전 정보. 요청 에러가 발생했을 때 사용자 환경을 알아보기 위해 사용한다.
- **Cookie**
  - 서버가 response의 Set-Cookie 헤더로 설정한 쿠키 정보
- **Referer**
  - 바로 직전에 머물렀던 웹 주소(유입 경로를 알아보기 위해 주로 사용됨)
- **Authorization**
  - 인증 토큰을 서버로 보낼 때 사용하는 헤더. API 요청을 할 때 토큰이 없으면 거절하므로 헤더 형태로 토큰을 같이 전송한다.  
  `Authorization: Bearer XXXXXXXX` 처럼 먼저 토큰의 종류를 알린 다음, 토큰 문자를 보낸다.
- **Origin**
  - 서버로 요청을 보낼 때, 요청이 어느 주소에서 시작되었는지 나타낸다. 요청을 보낸 주소와 받는 주소가 다르면 CORS 문제가 발생하기도 한다.
  - Origin은 scheme, hostname, port (프로토콜(http, https), 도메인 이름, 포트)로 구성되어 있다.  
  - CORS 문제가 발생하지 않으려면 둘의 Origin이 프로토콜, 도메인 이름, 포트까지 완전히 같아야 한다.
  - referer과 다른 점은 referer는 하위 경로와 쿼리 스트링까지 포함하지만 origin은 모든 경로가 나타나지 않는다는 점이다.
- **Accept**
  - 클라이언트 자신이 원하는 파일의 타입을 지정할 수 있다.
  - 이 밖에 Accept-[Charset/Encoding/Language]를 통해 원하는 인코딩 형태나 언어를 설정할 수도 있다.

## 4. Response header
- **Location**
  - 리소스가 리다이렉트된 때에(상태코드가 300번대인 경우) 브라우저가 어느 페이지로 이동해야 할 지 주소를 알려준다.
- **Set-Cookie**
  - 서버측에서 클라이언트에게 세션 쿠키 정보를 설정
- **Allow**
  - 서버가 지원 가능한 HTTP 메소드의 리스트를 나타낸다.
  - HTTP 메소드 중 OPTIONS에 대한 응답이나 405 Method Not Allowed 에러에 대한 응답으로 이 헤더를 받아볼 수 있다.
  - `Allow: GET, HEAD`
- **Access-Control-Allow-Origin**
  - 접근을 허락할 대상의 주소를 설정한다.
  - 요청을 보내는 곳의 주소(Origin)와 응답을 해주는 곳의 주소가 다르면 cookie나 토큰을 악용하는 공격이 발생할 위험이 있기 때문에 브라우저는 기본적으로 Origin과는 다른 주소로 접근하는 것을 차단한다. 이런 CORS 에러를 방지하기 위해 서버가 Access-Control-Allow-Origin에 접근을 허락할 대상의 주소를 설정해 응답한다.  
  브라우저는 Origin과 Access-Control-Allow-Origin을 비교해 둘이 같다면 응답을 폐기하지 않는다. 따라서 CORS 에러가 일어나지 않는다.
   - `Access-Control-Allow-Origin: *` - 모든 주소의 cross-origin 접근을 허용 (편하긴 한데 보안이 취약해진다...)
   - `Access-Control-Allow-Origin: https://www.domain-A.com`  
  `https://www.domain-A.com` 사이트가 이 헤더를 반환하는 서버에게 요청을 했다면 CORS 에러가 나지 않는다.
- **Expires**
  - 응답해주는 리소스의 캐시가 만료되는 때를 알려준다. Cache-Control의 max-age 옵션과 중복되어 지정되어 있다면 expires를 무시한다.
  - `expires: Tue, 17 May 2022 12:42:19 GMT`
- **Age**
  - max-age 시간 내에서 얼마나 흘렀는지 초 단위로 알려준다.
- **Etag**
  - 컨텐츠가 바뀌었는지 검사하는데 사용되는 태그.
  - 캐시된 리소스의 Etag와 서버 리소스의 Etag를 비교해 서로 다르다면 컨텐츠가 바뀐 것을 인식하고 기존것을 삭제하고 새로운 것을 받아온다.

## Reference
- [MDN HTTP>Headers](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers)
- [HTTP Header 이번에 확실히 정리하고 가세요! - velog](https://velog.io/@jkijki12/HTTP-Header-%EC%A0%95%EB%A6%AC)
- [[Network] HTTP 헤더의 종류 및 항목](https://gmlwjd9405.github.io/2019/01/28/http-header-types.html)
- [[Secure Header] CSP(Contents-Security-Policy)](https://bziwnsizd.tistory.com/51)
- [CORS는 왜 이렇게 우리를 힘들게 하는걸까?](https://evan-moon.github.io/2020/05/21/about-cors/)