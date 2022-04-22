# Effect Hook
React 컴포넌트가 화면에 렌더링된 이후에 비동기로 처리되어야 하는 부수적인 효과들을 Side Effect라고 한다.  (예 - 외부 API 호출해 데이터 가져오기)

Side Effect 종류
1. 정리(clean-up)가 필요한 것 - API호출, setTimeout, setInterval
2. 정리가 필요하지 않은 것 - 네트워크 리퀘스트, DOM 수동 조작, 로깅 등

기존에는 클래스 기반 컴포넌트의 생명주기 메서드가 이런 부수적인 효과들을 담당했다.  
> - componentDidMount() - render()로 컴포넌트가 DOM에 mount된 다음에 실행되는 함수. 이때 주로 API 호출 등을 함
> - componentDidUpdate() - 갱신이 일어난 직후에 실행된다. 예를 들어 컴포넌트 갱신 후에 side effect도 같이 업데이트 해주거나 DOM을 조작할 때 이 메서드를 사용할 수 있다.  
> - componentWillUnmount() - 컴포넌트가 마운트 해제될 때 setTimeout이나 이벤트 리스너들을 제거하는 등의 정리 작업을 이 메서드 안에서 수행한다.

하지만 간단한 Side Effect를 구현하는데 복잡하게 여러 메서드를 작성해야 하고, 복잡도로 인해 오류가 발생할 가능성이 크다. 그리고 생명주기에 따라 로직이 작성되기 때문에 관련도가 높은 로직끼리 뭉쳐있지 않고 흩어져있게 되어 불편하다. 이를 useEffect() hook이 해결해준다.

`useEffect`는 `componentDidMount`와 `componentDidUpdate`, `componentWillUnmount`가 합쳐진 것이라고 생각해도 된다.

## 정리(Clean-up)를 이용하지 않는 Effects
React가 DOM을 업데이트한 뒤 추가로 코드를 실행해야 하는 경우가 있다.  
예를 네트워크 리퀘스트, DOM 수동 조작, 로깅이 있는데, 이것들은 정리가 필요 없다. class와 hook에서 이러한 side effects를 어떻게 다르게 구현하는지 살펴보자.

### Class 사용 예시
render() 메서드 실행 자체는 side effect를 발생시키지 않는다. effect를 수행하는 건 React가 DOM을 업데이트 한 *이후*이다. 그러므로 side effect를 `componentDidMount`와 `componentDidUpdate`에 두는 것이다.

```JSX
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {count: 0};
  }

  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
  }

  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count}</p>
        <button onClick={() => this.setState({count: this.state.count + 1})}>
          Click me
        </button>
      </div>
    );
  }
}
```
컴포넌트가 최초로 render()될 때 DOM에 컴포넌트가 mount되는데, 이 때 `componentDidMount`가 문서의 title을 설정해주고, state가 업데이트 되어서 재렌더링될 때 `componentDidUpdate`가 실행되어 문서 title이 재설정된다.  
두 개의 메서드에서 코드가 중복된다. 설령 중복되는 코드를 메서드로 따로 추출한다고 해도 여전히 두 개의 생명주기 메서드에서 함수를 호출해야 한다.

### Hook 사용 예시

```JSX
import React, { useState, useEffect } from 'react';
function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked { count }</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```
effect는 컴포넌트 마운팅과 업데이트의 구별 없이 렌더링 이후에 매번 실행된다. `componentDidMount`와 `componentDidUpdate`를 합친듯한 효과.

## 정리(Clean-up)을 이용하는 Effects
친구의 온라인 상태를 구독(Subscribe)할 수 있는 ChatAPI 모듈을 이용하는 예시이다. 이 경우에 메모리 누수가 발생하지 않도록 정리(clean-up)하는 것이 중요하다.
### Class 사용 예시
```JSX
class FriendStatus extends React.Component {
  constructor(props) {
    super(props);
    this.state = { isOnline: null };
    this.handleStatusChange = this.handleStatusChange.bind(this);
  }

  componentDidMount() {
    // ChatAPI: 친구의 온라인 상태를 구독(subscribe)할 수 있는 모듈
    // id로 친구를 검색해서 handleStatusChange에게 파라미터로 status를 넘겨줌
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  // 설명을 위해 componentDidUpdate()를 생략했다.
  // 이 부분을 작성하지 않으면 중간에 friend.id 업데이트로 인해 오류가 발생할 수 있다.


  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  handleStatusChange(status) {
    this.setState({
      isOnline: status.isOnline
    });
  }

  render() {
    if(this.status.isOnline === null) {
      return 'Loading...';
    }
    return this.state.isOnline ? 'Online' : 'Offline';
  }
}
```
componentDidMount와 componentWillUnmount가 개념상 똑같은 코드를 가지고 있음에도 불구하고 생명주기 메서드로 인해 둘을 분리해야 한다.

생략한 componentDidUpdate()가 궁금하다면 [effect가 업데이트 시마다 실행되는 이유](#effect가-업데이트-시마다-실행되는-이유)의 코드 참조



### Hook 사용 예시
```JSX
function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    // effect 이후에 정리(clean-up)할 방법 명시
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if(isOnline == null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```
이렇게 하면 결합도가 높은 구독(subscription)의 추가와 제거를 useEffect로 한번에 다룰 수 있다. 정리를 위해 필요한 함수는 `useEffect`에서 `return`하면 된다. 이 때 반환하는 함수는 무조건 유명함수(named function)일 필요가 없다. cleanup이라는 이름 말고 다른 이름을 가져도 되고 화살표 함수를 사용해도 된다. 정리가 필요하지 않은 경우에는 return문을 작성하지 않으면 된다.

### effect를 clean-up 하는 시점은 언제일까
effect는 앞에서 보았듯이 렌더링이 실행되는 때마다 다시 실행된다.  
재렌더링되면 이전 effect의 정리함수가 실행되면서 이전의 렌더링에서 파생된 effect를 정리한다. 그 다음에 새로운 effect가 실행된다.
### effect가 업데이트 시마다 실행되는 이유 
친구가 온라인인지 아닌지 표시해주는 FriendStatus 컴포넌트 예시를 생각해보자. props로 받은 friend.id를 이용해 친구의 온라인 상태를 읽어오는 effect를 수행한다. 그리고 컴포넌트가 언마운트 됐을 때 구독을 해지했다.  
만약 effect가 매번 다시 실행되지 않는다면 중간에 friend prop이 변했을 때 이전 친구의 온라인 상태를 계속 표시하게 될 것이다. 또한 마운트 해제가 일어날 때도 구독 취소가 다른 친구의 ID로 이루어져 메모리 누수나 충돌이 발생할 수 있다.

이 문제를 해결하기 위해 클래스 기반 컴포넌트에서는 componentDidUpdate를 이용해 업데이트가 일어날 때마다 이전 friend.id 구독을 해제하고 새로 바뀐 friend.id를 구독하는 중간 처리를 해줘야 했다. 그리고 이 과정을 빠뜨려서 버그가 흔하게 일어난다.

```JSX
  componentDidMount() {
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentDidUpdate(prevProps) {
    // 이전 friend.id에서 구독을 해지합니다.
    ChatAPI.unsubscribeFromFriendStatus(
      prevProps.friend.id,
      this.handleStatusChange
    );
    // 다음 friend.id를 구독합니다.
    ChatAPI.subscribeToFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }

  componentWillUnmount() {
    ChatAPI.unsubscribeFromFriendStatus(
      this.props.friend.id,
      this.handleStatusChange
    );
  }
```

> useEffect가 업데이트마다 다시 실행되는 덕분에 클래스 컴포넌트에서 업데이트 로직을 빼먹으면서 흔히 발생할 수 있는 버그를 예방할 수 있다.


## 🎈Tip
stateHook을 여러 번 사용할 수 있는 것처럼 effect도 여러번 사용할 수 있다. 따라서 서로 관련 없는 로직들을 갈라놓을 수 있다. 생명주기 메서드에 따라서가 아니라 *코드가 무엇을 하는지에 따라* 나눌 수 있는 것이다.

```JSX
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }
  //...
}
```