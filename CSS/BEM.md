# CSS 방법론 - BEM
## 1. 방법론이란?
CSS 클래스 네임을 어떻게 지으면 좋을지 고민하는 것

### CSS 방법론이 필요한 이유
1. 여러 명이 함께 작업 할 때 아케텍쳐에 대한 기준이 되어준다.
2. Cascade의 Specificity(명시도)에서 비롯되는 복잡성을 줄여준다.

방법론 중에서도 직관적이고 장점이 많은 BEM이 많이 쓰인다.

## 2. BEM 이란?
BEM은 Block, Element, Modifier의 앞글자를 딴 것이다.
BEM은 id나 attribute가 아니라 class를 selector로 사용하는 방법론이다. 그렇기 때문에 클래스 네임은 대상 엘리먼트가 속한 구조를 잘 표현하고 있어야 한다.

`.Block__Element--Modifier`의 형태로 class 이름을 짓는다.

## 3. Block / Element / Modifier
### Block
기능적으로 독립적인 재사용 가능한 컴포넌트이다.  
예를 들어 input과 button이 쌍을 이뤄 사용자가 검색을 할 수 있게 해주는 컴포넌트는 search block이라고 할 수 있다. 또한 head block 안에 search block이 포함될 수 있는 것처럼 블록이 블록을 감쌀 수 있다.

### Element
블록을 구성하면서 의미적으로 블록에 종속된 요소를 의미한다.  
예를 들어 search-form을 구성하고 있는 input 요소는 search-form 바깥에서는 의미를 잃어버리므로 element이다.

또한 엘리먼트 이름은 '어떻게 보이는지'가 아니라 '어떤 목적인가'에 따라 이름을 부여해야 한다. 예를 들면 에러 메세지를 표시하는 문구에 `.red`라는 이름을 주는 것이 아니라 `.error`라는 이름을 줘야 한다.

input이 search-form에 종속적이라는 것을 나타내주기 위해 `.search-form__input`처럼 작성한다.

```html
<form class="search-form">
    <div class="search-form__content">
        <input class="search-form__input">
        <button class="search-form__button">Search</button>
    </div>
</form>
```
엘리먼트 이름은 무조건 태그와 같은 것으로 붙이지 않아도 된다.  `.tab`블럭을 이루고 있는 \<li> 태그에 `.tab__item` 같이 클래스 이름을 붙여줄 수 있다.

그리고 만약 엘리먼트가 중첩되어 있다면, 자식 엘리먼트는 `.block__element1__element2`처럼 엘리먼트의 자식으로 취급하는 것이 아니라 `.block__element2`처럼 block의 자식으로 취급한다.

덕분에 중첩되어 안에 있던 엘리먼트가 바깥으로 나오는 등의 구조 변화가 생기더라도 구조에 종속되지 않을 수 있다.

```html
<!-- 틀린 예 -->
<form class="search-form">
    <div class="search-form__content">
        <input class="search-form__content__input">
        <button class="search-form__content__button">Search</button>
    </div>
</form>
```

```html
<!-- 올바른 표기법 -->
<form class="search-form">
    <div class="search-form__content">
        <input class="search-form__input">
        <button class="search-form__button">Search</button>
    </div>
</form>
```

한 가지 주의할 점은 블럭 안에도 블럭이 올 수 있다는 점이다.
`.sns`와 `.nav`는 `.header` 바깥에서 독립적으로 쓰일 수 있으므로 엘리먼트가 아니라 블럭으로 지정할 수 있다.
```html
<div class="header">
	<div class="header__inner">
		<div class="sns"></div>
		<div class="header__logo"></div>
		<div class="header__auth"></div>
		<div class="nav"></div>
		<div class="header__search"></div>
	</div>
</div>
```

### Modifier
블럭이나 엘리먼트의 속성을 표시한다. 기본형에서 파생된 조금 특수한 블럭이나 엘리먼트에게 붙인다. 예를 들면 checked, focused, error 등이 이에 해당한다.

주의할 점은 Modifier에 작성한 속성은 기본형을 확장한 형태이기 때문에 원본 클래스는 유지하고 추가로 클래스를 더해야 한다.
`tab__item tab__item--focused`  
tab__item 엘리먼트가 focused 된 경우를 나타내는 클래스

#### Modifier 타입
1. Boolean
focused나 checked 가 boolean 타입에 해당한다. focused처럼 값이 true라고 가정하고 modifier를 붙인다.
```html	
<ul class="tab">
  <li class="tab__item tab__item--focused">탭 01</li>
  <li class="tab__item">탭 02</li>
  <li class="tab__item">탭 03</li>
</ul>
```

2. Key-value
`color-gray`나 `theme-normal`처럼 `속성-값`을 표시할 때 사용한다. 주로 테마와 다크모드를 나타낼 때 많이 쓰인다.

```html
<div class="column">
  <strong class="title">일반 로그인</strong>
  <form class="form-login form-login--theme-normal">
    <input type="text" class="form-login__id"/>
    <input type="password" class="form-login__password"/>
  </form>
</div>
 
<div class="column">
  <strong class="title title--color-gray">VIP 로그인</strong>
  <form class="form-login form-login--theme-special form-login--disabled">
      <input type="text" class="form-login__id"/>
      <input type="password" class="form-login__password"/>
  </form>
</div>
```

## BEM 사용 이점
이제 BEM을 사용하면 어떤 점이 좋은지 말할 수 있다.😎
1. **모듈화, 재사용성**  
기존에는 `부모 > 자식` 같은 selector가 있을 때 html 구조가 바뀐다면 selector도 다시 작성해줘야 하는 등 css가 구조에 종속적이었는데 클래스 네임만으로 엘리먼트를 선택할 수 있기 때문에 각 엘리먼트의 독립성이 늘어났다. 따라서 CSS 파일을 라이브러리처럼 사용할 수 있다.

2. **구조화**  
클래스 이름만 보고도 자연스레 해당 엘리먼트의 구조를 알 수 있다.

3. **SASS에서 괄호 중첩 사용 줄일 수 있음**  
엘리먼트가 중첩되어도 블럭의 독립된 요소로 취급하므로 괄호 중첩이 덜해서 보기 쉽다.
```css
/* 기존 방식 */
.header {
  .nav {
      position: absolute;
      .list {
        list-style: none;
      }
      .item {
        outline: 0;
        .link {
            color: red;
        }
      }
  }
}

/* BEM */
.header {
  &__nav {
      position: absolute;
  }
  &__list {
      color: red;
  }
  &__item {
      outline: 0;
  }
  &__link {
      color: red;
  }
} 
```