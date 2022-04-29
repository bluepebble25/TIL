# Viewport
뷰포트(viewport)란, 웹 페이지에서 사용자의 보이는 영역(visible area)를 말한다.

## viewport를 지정해야 하는 이유
1. meta tag에 viewport에 대한 별다른 내용이 없으면 기본적으로 웹 페이지를 980px을 기준으로 렌더링 하게 된다.
2. 그러면 980px보다 작은 디바이스에서는 모든 내용을 보여주기 위해 브라우저가 화면 배율을 축소해서 보여주므로 가독성이 떨어진다.
3. 이를 해결하기 위해 viewport의 너비를 설정해줘야 한다. 만약 디바이스 너비가 640px이라면 `<meta name="viewport" content="640, initial-scale=1.0">`처럼 설정해 화면을 640px 기준으로 렌더링해서 보여줄 수 있다.
4. 하지만 이렇게 한다면 모든 기기에 대응할 수 없으므로 width를 device-width로 설정한다. `<meta name="viewport" content="width=device-width, initial-scale=1.0">`
5. 그러면 모든 장비에서 1배율의 크기로 같은 화면을 볼 수 있다. 예를 들어 640px, 세로 640px의 이미지를 렌더링한다고 하자. 그보다 큰 화면에서는 모두 이미지가 640px X 640px 크기로 똑같이 보이고, 그보다 작은 화면에서는 이미지가 보이는 범위가 일부여서 스크롤을 할지라도 마찬가지로 같은 크기의 이미지가 보인다.

## viewport 속성
| 속성          | 설명 |
|---------------|-----|
| width         | 뷰포트의 가로 크기          |
| initial-scale | 페이지 접속 시 초기 배율    |
| user-scalable | 사용자의 축소/확대 허용 여부. 초기값은 yes, no는 금지 |
| maximum-scale | 뷰포트의 최대 배율값 (0~10) |
| minimum-scale | 뷰포트의 최소 배율값 (0~10) |

<br>

[naver 모바일](https://m.naver.com/)의 메타 태그를 살펴보면
```html
<meta name="viewport" content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no">
```
이렇게 되어있다. 모바일에 최적화된 UI를 제공하기 때문에 사용자가 확대/축소하지 못하게 `user-scable`을 no로 지정하고 `minium-scale`과 `maximum-scale`도 1로 설정해 화면을 고정하고 있다.

반응형이 아니라 각 디바이스마다 최적화된 다른 화면을 보여주는 방식(적응형)으로 개발한다면 이 방식을 쓰면 된다.
