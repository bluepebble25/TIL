# package.json, 그리고 틸트(~)와 캐럿(^)
> 목차
> 1. [package.json](#packagejson)
> 2. [버전 범위 지정 (틸트, 캐럿)](#버전-범위-지정-틸트-캐럿)

## package.json
`npm init`을 하면 프로젝트 폴더에 해당 프로젝트/패키지의 정보를 담고 있는 package.json이 생성된다. 그래서 나중에 배포된 패키지를 `npm install [패키지명]`을 하게 되면 해당 패키지의 package.json의 `dependencies`나 `devDependencies`를 참조해 그 패키지가 의존하고 있는 다른 패키지까지 같이 다운받아준다. `npm install [패키지명]@[버전]`을 하면 특정 버전을 다운받을 수 있다. package.json의 `name`과 `version` 항목 덕분에 패키지와 패키지의 버전을 식별할 수 있는 것이다.

예시
```json
{
  "name": "myproject",
  "version": "1.0.0",
  "description": "description of project",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

### name, version
name과 version은 필수
npm에 배포할 거라면 name은 겹치지 않아야 한다.  
name과 version으로 패키지의 고유성을 판별하므로 내용이 바뀐다면 반드시 version도 바뀌어야 한다. version은 [semantic versioning](https://kiwinam.com/posts/33/version-naming/) 규칙을 따라야 한다. 버전 비교를 string으로 하게 되면 `'1.5.10' >= '1.5.9'`이 `false`가 되는 등의 예외가 발생하기도 하고, 버전이 규칙을 잘 따르는지 유효성 검사를 해야 하는데, 이런 고민을 덜어주는 버전관리 npm 라이브러리 [semver](https://github.com/npm/node-semver#readme)가 있다.

### description
문자열로 기술한다. `npm search [검색어]`를 할 때 리스트에 표시되는 설명이다. 사람들이 패키지를 찾아내고 패키지에 대해 이해할 때 도움이 된다.

### keywords
문자열 배열로 기술한다. `npm search [검색어]`할 때 리스트에 표시되는 키워드이다. 사람들이 패키지를 찾아내고 패키지에 대해 이해할 때 도움이 된다.

```json
{
  //"...": "...",
  "keywords": [
    "async",
    "promise"
  ]
}
```

### main
패키지의 시작점이 될 파일을 설정한다.
```json
"main": "server.json"
```

### scripts
명령어와 그때 실행할 동작의 쌍을 모아놓은 것이다.

```json
{
  //"...": "...",
  "scripts": {
    "start": "node server.js",
    "build": "webpack --watch"
  }
}
```

이렇게 등록해놓으면 `npm run build`를 하면 `webpack -- watch`를 입력하지 않아도 간단한 명령어로 동작을 실행할 수 있다.  
react 프로젝트에서 `npm run start`만으로도 `react-scripts start`를 대신할 수 있는 것은 scripts에 해당 명령어와 그에 대한 동작이 등록되어 있기 때문이다.

### private
`npm login`으로 로그인 하고 `npm publish`를 입력하면 자신이 만든 패키지를 배포할 수 있다.  
`"private" : true`로 설정하면 `npm publish` 명령어를 거부하게 된다. 외부에 패키지를 공개하고 싶지 않을 때 설정한다.

### author
```json
"author": "[이름]"
```

혹은
```json
{
  "author": { 
    "name": "[이름]",
    "email": "abc@gmail.com",
    "url": "[site URL]"
  }
}
```

하나로 줄이고 싶다면 다음과 같이 줄여서 작성할 수 있다. npm이 알아서 파싱해준다.
```json
"author": "[이름] <이메일> (site URL)"
```

### license
다른 사용자들이 이 패키지를 사용할 때 어떤 사항을 지켜야 하는지 라이센스로 명시한다.
```json
  "license": "MIT"
```

### bugs
버그를 리포트 할 곳에 대한 정보(url, email)
```json
"bugs": {
  "url": "http://github.com/owner/project/issues",
  "email": "bugreport@hostname.com"
}
```

### repository
소스코드가 저장되는 저장소. 다른 사람들이 기여할 수 있도록 정보를 준다.
```json
"repository": {
    "type": "git",
    "url": "http://github.com/npm/npm.git"
}
```

### homepage
해당 애플리케이션이 호스팅된다면 루트를 어디로 삼을지 설정하는 항목이다.

React 앱을 호스팅할 때를 예로 들면, Create-React-App은 빌드 결과물이 서버의 루트에서 호스팅된다고 가정하고 빌드한다. 이를 재정의하고 싶을 때 homepage 항목에 다음과 같이 지정한다.
```json
"homepage": "http://mywebsite.com/myapplication"
```

### dependencies, devDependencies
이 패키지가 의존하고 있는 다른 패키지와 버전이 여기에 적히게 된다.
어떤 라이브러리가 로직과 화면 구성 등 런타임에 사용되는 것이라면 `dependencies`에, 개발에 필요한 라이브러리라면(ex-빌드 단계에만 필요한 Babel같은 컴파일러) `devDependencies`에 적힌다.

이 설정은 개발자가 직접 적을 수도 있지만 프로젝트를 생성하고 라이브러리를 세팅할 때
```js
'npm install [패키지명] --save-prod' // -P도 OK (prod는 production의 약자)
'npm install [패키지명] --save-dev' // -D도 OK
```
를 이용해 다운로드 하면 알아서 package.json의 `dependencies`나 `devDependencies`에 기록된다.

누군가 배포한 애플리케이션을 다운받으려 하는 일반 사용자는 개발용 라이브러리는 필요 없으므로 `npm install [패키지명] --save-prod`을 해서 `dependencies`에 있는 것만 받을 수 있다. 그러면 node_modules가 무거워지지 않는다.

아무 옵션 없이 `npm install`로 패키지를 설치하면 `dependencies`와 `devDependencies`에 있는 라이브러리가 모두 다운되어 node_modules에 위치한다. 옵션을 이용해 다운받든 아니든 라이브러리가 node_modules에 다운되는 것은 마찬가지다. 다만 프로덕션용과 개발용으로 의존 라이브러리 목록을 나누어놓는다면 사용자가 다운받을 때 편의를 줄 수 있기 때문에 사용한다.

## 버전 범위 지정 (틸트, 캐럿)
`dependencies`나 `devDependencies`를 보면 `^`이나 `~`를 볼 수 있다. 만약 의존 라이브러리의 버전이 너무 다르다면 패키지가 제대로 작동하지 않을 수 있기 때문에 어느 버전의 라이브러리까지 허용할 지 지정하는 것이다. 이를 version range라고 한다.

`Major.Minor.Patch` 중 minor와 patch의 변경을 허용할 건지, 아니면 patch만 허용할 건지, 딱 정해진 버전만 사용할 수 있는지 `^`이나 `~`를 이용해 자세하게 설정할 수 있다.

```json
{
  "dependencies": {
    "@testing-library/jest-dom": "^5.16.4",
    "@testing-library/react": "^13.1.1",
    "@testing-library/user-event": "^13.5.0",
    "react": "^18.0.0",
    "react-dom": "^18.0.0",
    "react-scripts": "5.0.1",
    "web-vitals": "^2.1.4"
  }
}
```

### ~(틸드)
Major.Minor.Patch에서

> minor version이 지정 X -> minor-level 변경 허용  
> minor version이 지정 O -> patch level 변경 허용

- ~1.2.3 / ~1.2
  - minor 지정되어 있으므로 patch level만 변경 가능
  - 1.2.0 이상 1.3.0 미만
- ~1
  - minor 지정 안돼있으므로 minor level 변경 허용
  - 1.0.0 이상 2.0.0 미만

### ^(캐럿)
왼쪽에서부터 봤을 때 0이 아닌 곳을 시작점으로 삼아 판단한다. 0이 아닌 시작점보다 아래 단계의 변화를 허용한다.  
예를 들어 `^0.2.3`에서 0이 아닌 맨 왼쪽 시작점은 2이다. 이 위치는 minor이기 때문에 그 아랫단계인 patch의 변경을 허용한다.

- ^1.2.3
  - 0이 아닌 맨 왼쪽 시작점이 1이고, major 위치이므로 minor와 patch의 변경을 허용한다.
- ^0.2.3`
  - 0이 아닌 맨 왼쪽 시작점은 2이다. 이 위치는 minor이기 때문에 patch의 변경을 허용한다.
- ^0.0.3
  - 0이 아닌 맨 왼쪽 시작점이 3이고 patch 위치이므로 버전 변경을 허용하지 않는다. 0.0.3만 쓸 수 있다.

patch 값이 누락되고 둘 다 major와 minor 둘 다 0으로 시작하면 (0.0, 0.0.x)  
patch 범위 내에서 변경 가능
- 0.0, 
  - 0.0.0 이상 0.1.0 미만
- 0.0.x
  - 위와 동일

## Reference
- [Software 버전 관리 규칙, 너만 모르는 Semantic versioning](https://kiwinam.com/posts/33/version-naming/)
- [Semantic Versioning 소개](https://spoqa.github.io/2012/12/18/semantic-versioning.html)
- [[NodeJs] 모두 알지만 모두 모르는 package.json](https://programmingsummaries.tistory.com/385)
- [[Node.js] - npm install, 이제는 알고 쓰자](https://c17an.netlify.app/blog/node.js/npm-install-%EC%A0%95%EB%A6%AC/article/)
- [npm semver - 틸트 범위(~)와 캐럿 범위(^)](https://velog.io/@slaslaya/npm-semver-%ED%8B%B8%ED%8A%B8-%EB%B2%94%EC%9C%84%EC%99%80-%EC%BA%90%EB%9F%BF-%EB%B2%94%EC%9C%84)