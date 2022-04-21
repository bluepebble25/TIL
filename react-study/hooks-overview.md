# Hook 개요

- 목차
  - [왜 Hook을 사용하는가?](#왜-hook을-사용하는가)
  - [State Hook](#state-hook)
  - [Effect Hook](#effect-hook)
  - [Hook 사용 규칙](#hook-사용-규칙)
  - [나만의 Hook 만들기](#나만의-hook-만들기)

## 왜 Hook을 사용하는가?
Hook은 React 16.8부터 새로 도입되었다. Hook은 이전까지는 클래스형 컴포넌트에서만 사용 가능했던 state나 생명주기 함수를 함수형 컴포넌트에서도 사용할 수 있게 해준다.

기존에 class형 컴포넌트는 다음과 같은 불편한 점이 있었다.
1. state를 관리하려면 constructor() 를 작성해줘야 한다.
2. this가 가리키는 대상이 무엇인지 신경써야 하며 이벤트 핸들러에 this 바인딩을 해줘야 한다.
3. 기존에는 자주 쓰이는 로직을 컴포넌트들간에 재사용하기 위해 HOC(High order component) 기법을 사용했는데, 많은 HOC가 중첩되는 HOC 헬이 발생할 수 있다.

이 문제는 hook을 사용하면 해결할 수 있다.

> Hook이란?  
> 함수 컴포넌트에서 state와 생명주기 함수를 사용할 수 있게 해주는 함수들을 hook이라고 한다. 내장 함수로는 `useState()`와 `useEffect()` 등이 있고 사용자가 자신이 원하는 기능을 갖는 hook을 새로 만들 수도 있다.

## State Hook
### useState() 
함수 기반 컴포넌트에서는 state 값들을 한꺼번에 모아서 관리하는 것이 아니라 state 값마다 개별의 변수로 관리한다. 그리고 각 state 값들은 자신을 변경할 수 있는 함수를 짝으로 가진다. useState()를 이용해 state 변수를 선언한다.

`const [<상태 값 저장 변수>, <상태 값 갱신 함수>] = useState(<상태 초기 값>);`

useState()를 실행하면 상태값을 저장할 수 있는 변수와 상태값 변경 함수를 배열 형태로 반환한다.  
상태값 업데이트 함수는 set으로 시작하게 이름짓는 것이 관습이다.
```JSX
import React, { useState } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked { count } times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}

ReactDOM.render(<Example />, document.getElementById('root'));
```
클래스 기반 컴포넌트에서는 `this.setState({count: this.count + 1})`로 state를 업데이트하고 `<p>You clicked { this.state.count } times</p>`로 state 값을 참조하는데, 이것보다는 `setCount(count + 1)`와 `<p>You clicked { count } times</p>`처럼 작성하는 것이 더 직관적라는 장점이 있다.

### setState()와 useState()의 차이
- setState()로 특정 값을 변경하면 이전 state에 새로운 state가 '합쳐'진다.  
  예를 들면 기존에 `state가 {size: 'M', color: 'black'}`일 때 `this.setState({size: 'S'})`를 하면 size 변수의 값이 변경되고, 변경된 부분이 기존의 state에 합쳐져 `{size: 'S', color: 'black'}`이 된다.  
- useState()로 생성된 state 변경 함수로 값을 변경하면 새로운 state로 대체(replace) 된다.  
  기존의 state가 `{size: 'M', color: 'black'}`이고 state 변경 함수가 `setCloth()`라고 할 때,  
  `setCloth({size: 'S'})`를 하면 state가 `{size: 'S'}`이라는 새로운 state로 대체(replace)되어 다른 값들은 사라진다.  
  기존의 값을 유지하면서 일부만 변경하고 싶다면 spread operator를 사용하면 된다.
  `setCloth(...cloth, size: 'S', productNum: 100)`처럼 deep copy로 기존 값을 유지하면서 일부를 수정하거나 다른 값을 추가할 수 있다.

### state 변수 여러 개 등록
상태값 여러개를 등록하려면 stateHook 여러 개를 작성하면 된다.
```JSX
function ExampleWithManyStates() {
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
  // ...
}
```

## Effect Hook
React 컴포넌트가 화면에 렌더링된 이후에 비동기로 처리되어야 하는 부수적인 효과들을 Side Effect라고 한다.  (예 - 외부 API 호출해 데이터 가져오기)

Side Effect 종류
1. 정리(clean-up)가 필요한 것 - API호출, setTimeout, setInterval
2. 정리가 필요하지 않은 것 - 네트워크 리퀘스트, DOM 수동 조작, 로깅 등

기존에는 Class 컴포넌트의 생명주기 메서드가 이런 부수적인 효과들을 담당했다.  
> - componentDidMount() - render()로 컴포넌트가 DOM에 mount된 다음에 실행되는 함수. 이때 주로 API 호출 등을 함
> - componentDidUpdate() - 갱신이 일어난 직후에 실행된다. 예를 들어 컴포넌트 갱신 후에 side effect도 같이 업데이트 해주거나 DOM을 조작할 때 이 메서드를 사용할 수 있다.  
> - componentWillUnmount() - 컴포넌트가 마운트 해제될 때 setTimeout이나 이벤트 리스너들을 제거하는 등의 정리 작업을 이 메서드 안에서 수행한다.

하지만 간단한 Side Effect를 구현하는데 복잡하게 여러 메서드를 작성해야 하고, 복잡도로 인해 오류가 발생할 가능성이 크다. 그리고 생명주기에 따라 로직이 작성되기 때문에 관련도가 높은 로직끼리 뭉쳐있지 않고 흩어져있게 되어 불편하다. 이를 useEffect() hook이 해결해준다.

`useEffect`는 `componentDidMount`와 `componentDidUpdate`, `componentWillUnmount`가 합쳐진 것이라고 생각해도 된다.

### `componentDidMount`, `componentDidUpdate` 한번에
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

### `componentDidMount`, `componentDidUpdate`, `componentWillUnmount` 한번에
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

위의 코드와 Effect Hook에 대한 자세한 설명을 보려면 [Effect Hook 참조](hooks-effect.md)

## Hook 사용 규칙
1. 최상위에서만 Hook을 호출해야 한다. 반복문, 조건문, 중첩된 함수 내에서 Hook 사용 X
2. React 함수 컴포넌트 내에서만 Hook을 호출해야 한다. 일반 JS 함수에서는 X

이 두가지 규칙을 강제하기 위해 eslint-plugin-react-hooks라는 linter plugin을 제공하고 있다. 이 플러그인은 기본적으로 create react app에 포함되어 있다.

두 가지 규칙을 지켜야 하는 자세한 이유는 [Hook 사용 규칙 참조](hooks-rules.md)

## 나만의 Hook 만들기
상태 관련 로직만 재사용하고 싶을 때 전통적으로 HOC, render props를 썼다. 이 둘은 컴포넌트가 로직을 갖는 형태이다. Custom Hook을 사용하면 컴포넌트 트리에 새 컴포넌트를 추가하지 않고도 로직 재사용이 가능해진다.

###  예제로 Custom Hook 알아보기
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
`FriendStatus` 컴포넌트에서 친구의 접속 상태를 구독하는 로직을 다른 곳에서 재사용하기 위해 분리해보자.

`useFriendStatus`라는 custom Hook으로 분리했다.
```JSX
function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```
이 Hook은 `friendId`를 인자로 받아서 친구의 접속 상태를 반환해준다. 다음은 여러 컴포넌트에서 Hook을 사용하는 예시이다.

```JSX
function FriendStatus(props) {
  const isOnline = useFriendStatus(props.friend.id);

  if(isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

```JSX
function FriendListItem(props) {
  const isOnline = useFriendStatus(props.friend.id);

  return (
    <li style={{ color: isOnline ? 'green' : 'black' }}>
      {props.friend.name}
    </li>
  );
}
```

Hook은 상태 로직을 재사용하는 방법인 것이다. 각각의 Hook 호출은 도립된 state를 가진다. 그래서 심지어 한 컴포넌트 안에서 같은 custom Hook을 두 번 쓸 수도 있다.

### Custom Hook 이름 짓기
Custom Hook은 특별한 것이라기보다는 컨벤션에 가깝다. 이름이 "use"로 시작하고, 안에서 다른 Hook을 호출한다면 그 함수를 custom Hook이라고 부르는 것이다. `use`로 시작하는 네이밍 컨벤션은 linter 플러그인이 Hook을 인식하고 버그를 찾을 수 있게 해준다.