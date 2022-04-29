# meta 태그
meta 태그란 웹 문서에 대한 정보를 포함하고 있는 태그이다. 여기서 제공된 정보를 브라우저와 검색엔진에서 사용한다.

VSC에서 html:5을 입력하면 제공되는 템플릿을 살펴보자.
```html
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  
</body>
```
간단하게 살펴보면 인코딩 방식과 인터넷 익스플로러 브라우저의 렌더링 버전을 edge에 맞춰서, viewport(화면 크기와 초기 배율)을 디바이스 너비에 맞춰 1배율로 설정하고 있다.

## meta 태그 속성
charset, http-equiv, name, content가 있다.
1. charset="인코딩 방식"
2. http-equiv="설정할 속성"
3. name="정보명"
4. content="정보값"

http-equiv와 content, name과 content는 key-value의 관계이다.

## http-equiv
`<meta http-equiv="", content="">`의 형태로 사용한다.  
주로 웹 브라우저의 동작이나 http 속성을 설정하는 것과 관련있다.
| http-equiv      | content                      | 설명                                                                             |
|-----------------|------------------------------|--------------------------------------|
| X-UA-Compatible | IE=edge                      | 호환성 보기 설정 - 화면을 렌더링하는 방식을 최신 익스플로러 버전인 edge에 맞춰라 |
| content-type    | "text/html; charcet = UTF-8" | html4에서 인코딩 방식 설정할 때 사용했던 속성값. charset 속성이 `<meta charset="UTF-8">` 처럼 대체한다. |
| refresh         | 새로고침 간격(초)            | 해당 문서를 새로고침(refresh)할 시간 간격을 명시. "15;url:주소" 처럼 뒤에 이동할 URL 주소를 명시할 수도 있다. |
| Expires         | 캐쉬 만료일  | 캐시가 지정한 날짜 이후에 만료된다는 것을 알려준다. http 헤더의 *Cache-Control: max-age=[시간]* 대신 사용할 수 있음 |

## name
`<meta name="", content="">`의 형태로 사용한다.

| name         | content               |
|--------------|-----------------------|
| viewport     | width=device-width,initial-scale=1 (가로크기, 초기 확대배율) |
| keywords     | 키워드 콤마(,)로 나열 (검색에 사용됨) |
| description  | 문서 요약 (검색에 사용됨)           |
| robots       | 로봇 접근 설정 (자세한 사항은 [여기서](https://hunit.tistory.com/312))       |
| title        | 문서 제목              |
| author       | 작성자명               |
| copyright    | 저작권자               |
| date         | 제작 일시              |
| publisher    | 문서 발행 주체(회사나 단체) |
| format-detection | "telephone=no, address=no, email=no" (모바일에서 자동으로 전화번호, 이메일에 링크 걸리는 것 방지)|
| other agent  | 웹책임자 이름          |
| reply-to     | 문서 작성자에 대한 회신 주소 |
| email        | ``                    |
| filename     | 문서 파일의 이름       |
| distribution | 문서 배포자            |
| location     | 문서 제작 위치         |
| generator    | 제작도구 (예-Visual Studio Code) |

## Reference
- https://inpa.tistory.com/entry/HTML-%F0%9F%93%9A-meta-%ED%83%9C%EA%B7%B8-%EC%A0%95%EB%A6%AC
- https://blog.munilive.com/posts/meta-tag-property-and-use-method.html
- https://toss.tech/article/smart-web-service-cache