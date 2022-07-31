# HOC vs 커스텀 훅

## HOC란?
Higher Order Component(고차 컴포넌트)의 약자로 공통되는 로직을 분리해서 갖고 있으며, 다른 컴포넌트가 그 로직을 사용할 수 있도록 겉에 감싸주는 컴포넌트를 의미한다. wrapper 컴포넌트라고 하기도 한다.  
HOC에 대해 더 정확히 말하자면 HOC는 함수지만, 감싸일 대상 컴포넌트를 인자로 주면 그 컴포넌트를 감싼 컴포넌트를 생성하는 식이므로 고차 컴포넌트라고 하는 것이다.

### 예제
user 정보를 가져와서 state에 저장하고 인증 여부를 체크하는 로직이 여러 페이지에서 반복된다고 하자. state와 로직이 여러 페이지에서 반복되므로 이를 `withUserAuthHOC`로 분리하기로 마음먹었다. 이름을 `with뭐시기`로 짓는게 암묵적인 규칙이다.

```js
function withUserAuthHOC(WrappedComponent) {
  return class withuserAuthHOC extends React.Component {
    state = {
        user: {}
    };

    // 어쩌구저쩌구 인증 체크 로직
    // ...
    // ...

    render() {
      return (
        <WrappedComponent
          {...this.props}
          {...this.state}
        />
        // HOC 함수의 인자로 받은 컴포넌트에 로직의 결과로 생성된 결과물을 props로 내려준다.
      )
    }
  }
}
```
그리고 HOC를 다음과 같이 활용한다. 아래의 예제는 라우터에서 각 페이지를 HOC로 감싼 것이다. 참고로 router v5에서는 라우터에 바로 `{withUserAuthHOC(<LandingPage />)}`로 엘리먼트를 넣는게 가능했는데 v6에서는 변수에 할당해서 넣어야 한다.

```js
function App() {
  const AuthLandingPage = withUserAuthHOC(LandingPage, null);
  const AuthLoginPage = withUserAuthHOC(LoginPage, false);
  const AuthRegisterPage = withUserAuthHOC(RegisterPage, false);

  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<AuthLandingPage />} />
        <Route path="/login" element={<AuthLoginPage />} />
        <Route path="/register" element={<AuthRegisterPage />} />
      </Routes>
    </BrowserRouter>
  );
}
```

아니면 각 페이지를 만들어서 export 하는 단계에서 HOC로 감싸도 된다.

```js
function Apage({users}) {
  // {users}는 HOC가 내려준 props.users를 users라는 이름으로 사용하기 위해 구조분해할당을 사용한 것이다.
}

export default withUserAuthHOC(Apage)
```

### HOC는 꼭 클래스형 컴포넌트로 만들어야 하나
HOC는 꼭 클래스형 컴포넌트가 아니여도 된다. 과거의 hook이 나오기 전의 예제를 보면 HOC는 대부분 클래스형 컴포넌트로 등장했다. 함수형 컴포넌트로 만들지 않은 이유는 state와 생명주기함수를 사용하지 못하기 때문이었다. useState, useEffect 같은 훅이 나온 지금은 HOC를 함수형 컴포넌트로 구현해도 괜찮지만 커스텀 훅(로직을 분리한 함수)을 만들 수 있게 되었으므로 굳이 HOC 형태를 고집할 필요가 없다.

## 커스텀 훅(custom hook)이란?

### 탄생 배경
HOC가 여러개 중첩되게 된다면 index.html에 탑재될 때에는
```js
<LanguageHOC>
  <ThemeHOC>
    <AuthHOC>
      <Apage />
    </AuthHOC>
  <ThemeHOC>
</LanguageHOC>
```
처럼 될 것이다. 이를 콜백 지옥과 비슷한 용례로 HOC 지옥이라고 한다.
이런 문제를 해결하기 위해 공통된 로직을 컴포넌트에 분리하는 것이 아니라 함수로 분리해서 import로 간단하게 사용할 수 있는 커스텀 hook이라는게 생겼다.

커스텀 훅(custom hook)은 거창한 게 아니라 state와 로직을 갖는 함수이다.

기존에는 로직을 갖는 컴포넌트인 HOC가 페이지를 감싸서 필요한 정보를 내려주는 형태였다면, 커스텀 훅 덕분에 페이지 내에서 로직을 호출해서 사용할 수 있게 되었다.

### 커스텀 훅 만들기 예제
커스텀 훅은 `useState, useEffect`처럼 `use뭐시기`라고 짓는 것이 암묵적인 규칙이다. `withUserAuthHOC`의 커스텀 훅 버전인 `useAuth`를 만들기로 하자.

```js
function useAuth() {
  const [users, setUsers] = useState({});
  useEffect(() => {
    // user 정보 가져와서 state에 저장하고 인증 확인
  }, [userId]);

  return user;
}
```

```js
import useAuth from '../hoc/useAuth'

function Apage() {
  const user = useAuth();

  // A 페이지에서 user 정보 이용하는 부분
  return (...)
}
```