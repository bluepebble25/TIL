# 미디어 쿼리

> **목차**
> 1. [구성요소](#1-구성요소)  
>     - [미디어 유형](#11-미디어-유형)  
>     - [논리 연산자](#12-논리-연산자)  
>     - [미디어 특성](#13-미디어-특성)
>     - [사용 예](#사용-예)
> 2. [미디어 쿼리 적용법](#2-미디어-쿼리-적용법)  
> 3. [모바일 우선? 데스크탑 우선? ](#3-모바일-우선-데스크탑-우선)  
>     - [모바일 퍼스트](#모바일-퍼스트)  
>     - [데스트탑 퍼스트](#데스크탑-퍼스트)  
>     - [모바일 퍼스트 장단점](#모바일-퍼스트-장단점)
> 4. [breakpoint 결정법](#4-breakpoint-결정법)  
>     - [breakpoint를 어떻게 결정하는 것이 좋을까](#breakpoint를-어떻게-결정하는-것이-좋을까)  
>     - [분기점 리스트](#분기점-리스트)

특정 미디어 유형에서 특정 조건이 발생하면 레이아웃을 바꿔야할 때 사용한다.

```CSS
.title {
  font-size: 40px;
}
 
@media (max-width: 600px) {
  .title {
    font-size: 20px;
  }
}

```

폰트 크기가 40px인 title을 브라우저 창이 600px보다 작아지는 순간부터 20px로 렌더링한다.

## 1. 구성요소
미디어 쿼리는 미디어 유형, 논리 연산자, 미디어 특성으로 이루어져있다.

```css
@media 미디어유형 and (미디어특성){
	/* 해당 미디어 요소에서 적용할 CSS */
}
```

### 1.1 미디어 유형
미디어 쿼리를 적용할 미디어의 유형을 지정한다.

| 유형 | |
|------------|------|
|all     | 모든 미디어 타입 |
| print  |	인쇄 결과물 및 인쇄 미리보기 문서 |
| screen | 화면 대상 |

`@media screen {}`를 하면 스크린이 있는 디바이스에서만 적용된다.

### 1.2 논리 연산자
| 연산자 | |
|-----------|------------------------------------|
| and       | 모든 쿼리가 참이여야 적용            |
| not       |	모든 쿼리가 거짓이여야 적용          |
| , (comma) |	(= or) 어느 하나라도 참이면 적용      |
| only      | 미디어쿼리를 지원하는 브라우저만 적용 |

### 1.3 미디어 특성
분기문 같은 것이라고 생각하면 된다.
| 특성 | |
|--------------|------------------|
| width        | 뷰포트 너비       |
| height       | 뷰포트 높이       |
| aspect-ratio | 뷰포트 가로세로비  |
| orientation  | 뷰포트 방향 (가로모드/세로모드 판단) |

#### width/height
- `min-width/height`
- `max-width/height`

만약 조건이 `min-width: 780px`라면 최소 가로 780px 부터 적용된다는 뜻이다. 따라서 780px **이상**에서 이 구문이 적용된다.

만약 조건이 `max-width: 780px`라면 최대 가로 780px 까지는 적용된다는 뜻이다. 따라서 780px **이하**에서 이 구문이 적용된다.

> min-width는 이상, max-width는 이하

#### aspect-ratio
뷰포트의 가로 세로비
- `aspect-ratio` (같을 때)
- `min-aspect-ratio` (이상일 때)
- `max-aspect-ratio` (이하일 때)

```CSS
@media all and (aspect-ratio:5/4) { … } /* 뷰포트 너비가 5, 높이가 4 비율이면 실행 */
@media all and (min-aspect-ratio:5/4) { … } /* 뷰포트 너비가 5/4 비율 이상이면 실행 */
@media all and (max-aspect-ratio:5/4) { … } /* 뷰포트 너비가 5/4 비율 이하면 실행 */
```

#### orientation
뷰포트의 너비와 높이 중 어느 곳이 더 큰 지에 따라 세로모드인지 가로모드인지 판단한다.
- `potrait` (세로모드)
- `landscape` (가로모드)

```CSS
@media all and (orientation:portrait) { … } /* 세로 모드. 뷰포트의 높이가 너비에 비해 상대적으로 클 때 */
@media all and (orientation:landscape) { … } /* 가로 모드. 뷰포트의 너비가 높이에 비해 상대적 클 때 */
```


### 사용 예
- '모든 디바이스 & 너비가 768 이상 & 너비가 1080 이하'라는 조건을 모두 만족할 때 {} 안의 내용을 적용한다.
  ```CSS
  @media all and (min-width:768px) and (max-width:1080px) {}
  ```

- '높이가 680 이상'이거나 '세로 모드의 스크린 기기' 중 하나를 만족하는 경우에 적용
  ```CSS
  @media (min-height: 680px), screen and (orientation: portrait) { ... }
  ```

## 2. 미디어 쿼리 적용법
- CSS 파일 내에 직접 작성하는 방법

```CSS
@media (min-width:768px) {}
```

- \<link> 태그에 media 속성 작성하는 방법

```html
<link rel="stylesheet" media="all and (min-width:1200px)" href="desktop.css" >
<link rel="stylesheet" media="all and (min-width:768px) and (max-width:1199px)" href="laptop.css" >
<link rel="stylesheet" media="(max-width: 320px)" href="mobile.css" />
```
\<link>에 명시하는 두번째 방법은 디바이스에 따라 CSS 파일을 분리하고 싶을 때 주로 사용한다.  
하지만 CSS를 분리해 두더라도 브라우저가 CSS 파일을 모두 다운로드한다.  
HTTP 요청이 많아지면 성능이 그만큼 저하되므로, CSS 파일 내에서 @media로 분리하는 첫번째 방법을 권장한다.

---

## 3. 모바일 우선? 데스크탑 우선?
- 모바일 퍼스트 - `min-width` 사용 (사이즈가 클 수록 덮어쓰는 방식)
- 데스크탑 퍼스트 - `max-width` 사용 (사이즈가 작아질수록 덮어쓰는 방식)

### 모바일 퍼스트
```CSS
/* Mobile First */

/* 분기점(break point)을 낮은 순서대로 작성한다. */

.title { font-size: 12px; }
 
@media (min-width: 640px) {
  .title { font-size: 14px; }
}
 
@media (min-width: 768px) {
  .title { font-size: 16px; }
}
 
@media (min-width: 1024px) {
  .title { font-size: 18px; }
}
```
- 만약 기기 너비가 375px이라면 어떠한 미디어 쿼리 구문도 만족하지 못하므로 12px이 적용된다.
- 기기 너비가 820px이라면  640px 이상을 만족해 14px이 덮어씌워지고, 768px 이상도 만족하므로 16px이 덮어씌워진다. 하지만 1024px 이상은 만족하지 못하므로 최종적으로 768px이 된다.

### 데스크탑 퍼스트
```CSS
/* Desktop First */

/* 분기점을 높은 순서대로 작성한다. */
 
.title { font-size: 18px; }
 
@media (max-width: 1023px) {
  .title { font-size: 16px; }
}
 
@media (max-width: 767px) {
  .title { font-size: 14px; }
}
 
@media (max-width: 639px) {
  .title { font-size: 12px; }
}
```

### 모바일 퍼스트 장단점
데스크톱보다 모바일로 인터넷을 이용하는 경우가 많아지면서 모바일 우선으로 개발하는 움직임이 늘어나고 있다. 모바일 퍼스트 방식으로 개발하면 어떤 장단점이 있는지 살펴보자.

- 장점
  - 모바일 사용자 경험에 최적화되어 있다.
  - 웹사이트와 앱에 필수적인 요소만 남기도록 만든다.
  - 더 작고, 빠르고, 효율적인 서비스
  - 미적인 디자인보다 컨텐츠를 우선 생각하게 만든다.
- 단점
  - 데스크탑 버전이 간결하고 단순하게 보일 수 있다.
  - 개발은 데스크톱으로 한다. 개발하는데 있어서 더 어렵고 덜 직관적으로 느껴진다.
  - 창의성에 제약을 받을 수 있기 때문에, 다른 서비스와 두드러진 차이점을 보여주기 힘들다.

따라서 고객들이 주로 어느 기기를 통해 접속하는지 살펴보고 해당 디바이스 화면에 맞는 디자인과 개발을 우선 하는게 좋다.

## 4. Breakpoint 결정법

### Breakpoint를 어떻게 결정하는 것이 좋을까?

❌가장 안좋은 방법:  
  특정 기기를 기준으로 breakpoint를 결정하기 (예- 아이폰, 아이패드, 맥북의 화면 너비를 기준으로 개발)

❓그것보다 나은 방법:  
  비슷한 류의 기기를 한데 묶어서 그룹을 기준으로 breakpoint를 설정하기

두 방법의 문제점 - 모든 사람이 같은 기기를 사용하는 것이 아니고 조금이라도 화면 비율이나 해상도가 벗어났을 때 화면이 미묘하게 불편할 수 있다.

⭕가장 좋은 방법:  
  그룹별로 묶은 기기의 사이즈를 참조하되, 내 디자인을 기준으로 breakpoint 설정하기. 특정 화면너비에 디자인이 무너지면 거기에 새로운 breakpoint를 설정한다.  
  이렇게 하면 기기에 구애받지 않고 일관적인 디자인을 유지할 수 있다.

### 분기점 리스트
<table>
<thead>
  <tr>
    <th>디바이스</th>
    <th>특징</th>
    <th>사이즈</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td rowspan="2">모바일</td>
    <td>세로모드(Potrait)</td>
    <td>320px</td>
  </tr>
  <tr>
    <td>가로모드(Landscape)</td>
    <td>480px</td>
  </tr>
  <tr>
    <td rowspan="2">태블릿 세로모드(Potrait)</td>
    <td>소형</td>
    <td>600px</td>
  </tr>
  <tr>
    <td>일반</td>
    <td>768px</td>
  </tr>
  <tr>
    <td>태블릿 가로모드(Landscape),<br>작은 데스크톱</td>
    <td></td>
    <td>1024px</td>
  </tr>
  <tr>
    <td>데스크톱</td>
    <td></td>
    <td>1200px</td>
  </tr>
</tbody>
</table>

<br><br>

# Reference
- https://nykim.work/84?category=692675
- https://naradesign.github.io/media-query.html
- [[CSS] 모바일 우선 vs 데스크탑 우선Breakpoint](https://ujeon.medium.com/css-%EB%AA%A8%EB%B0%94%EC%9D%BC-%EC%9A%B0%EC%84%A0-vs-%EB%8D%B0%EC%8A%A4%ED%81%AC%ED%83%91-%EC%9A%B0%EC%84%A0breakpoint-3ed09d033c49)