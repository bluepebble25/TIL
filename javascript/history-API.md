# History API

## History 객체란?

history 객체는 window가 브라우저의 세션 기록(방문 기록)에 접근 할 수 있도록 하는 객체이다.  
window의 프로퍼티인 history는 원래 window.history로 접근해야 하지만 window는 전역객체이므로 생략할 수 있다.
React에서는 window.history로 window까지 명시해야 한다.

## 속성

|                           |                                                                                                                                                                                                   |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| history.length            | 방문 기록 스택의 크기(현재 페이지 포함) <br> 예) A,B,C 페이지를 차례대로 방문했고 현재 C 페이지에 있다면 history.length는 3이다. <br> B페이지로 뒤로가기해도 스택의 크기는 같으므로 값은 여전히 3 |
| history.scrollRestoration | 페이지 사이를 이동 시 이전 스크롤 위치를 복원할 지 설정 <br> 옵션값은 `auto(복원), manual(복원X)`                                                                                                 |
| history.state             | 기록 스택 최상단의 state 값을 반환한다. <br> popstate 이벤트를 기다리지 않고 현재 기록의 스테이트를 볼 수 있는 방법. state란 url, title, 페이지에 대한 데이터를 담고 있는 객체이다.               |

## 메소드

크게 두 가지 부류로 분류할 수 있다.

1. 페이지 이동 메서드
2. 세션 기록에 새 데이터를 추가할 수 있는 메서드

|                        |                                                                                                                                                                                                                                                                                                |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| history.back()         | 이전 페이지로 가기                                                                                                                                                                                                                                                                             |
| history.forward()      | 다음 페이지로 가기                                                                                                                                                                                                                                                                             |
| history.go()           | 매개변수 숫자만큼 페이지 이동 <br> history.go(-2) - 두 페이지 뒤로 / history.go(1) - 다음 페이지로 이동 <br> history.go(), history.go(0) - 둘 모두 같은 효과. 현재 페이지로 이동(새로고침)                                                                                                     |
| history.pushState()    | 우리가 이전 페이지, 다음 페이지로 이동하는 것은 사실 세션 스택이라는 일종의 목록에서 왔다갔다 하는 것이다. <br> **pushState()는 목록에 새로운 기록을 삽입한다.** 기록을 쌓는 것이므로 그 페이지로 이동되는 것은 아니다. <br> 매개변수로는 데이터를 담은 객체, 페이지 title, 경로를 줄 수 있다. |
| history.replaceState() | pushState()와 다르게 맨 위에 새로운 기록을 쌓는 것이 아니라 **맨 위에 있던 기록을 새 것으로 교체**한다. <br> 예를 들어 네이버 > 구글 > 유튜브 순으로 히스토리가 쌓여있는데 replaceState()를 통해 맨 위에 있는 유튜브를 인스타로 교체할 수 있는것이다. 그래서 네이버 > 구글 > 인스타 로 바뀐다. |

## history 변화 감지 이벤트

### onpopstate

state가 pop 되는 것을 감지하는 이벤트이다.

브라우저의 뒤로가기 버튼, history.back()에만 반응한다.

- 이 이벤트 - `history.pushState()`, `history.replaceState()` - 에는 반응하지 않는다.

`addEventListener` 혹은 `window.onpopstate = () => {};`로 이벤트를 등록하면 된다. 사용 예시로는 뒤로가기 방지 구현이 있다.

## Reference

- https://basemenks.tistory.com/269
- https://kdydesign.github.io/2020/10/06/spa-route-tutorial/
- https://www.zerocho.com/category/HTML&DOM/post/599d2fb635814200189fe1a7
- https://developer.mozilla.org/ko/docs/Web/API/Window/popstate_event
