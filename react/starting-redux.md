# Redux 시작하기
Redux는 상태 관리 라이브러리이다.

## 1. Redux를 쓰는 이유
React에서 데이터는 위에서 아래로만 흐를 수 있다. 그래서 일일이 props 내리기나 state 끌어올리기를 해야 한다. 이렇듯 React에서 컴포넌트끼리의 데이터 이동은 복잡한 단계를 거쳐야 하기 때문에 상태를 store라는 저장소에 한데 모아놓고 관리하는 개념이 생겼다. 상태를 가져오고 변경하는 것은 모두 store를 통한다.

그리고 상태 변경 로직도 컴포넌트에서 count + 1 처럼 직접 작성하지 않고 reducer란 곳에 미리 'INCREASE란 이름이 들어오면 `count + 1`을 저장해라' 처럼 명세해 놓기 때문에 만약 의도한 값이 나오지 않는 에러가 발생하면 컴포넌트 단이 아니라 상태 가공부분(reducer)를 디버깅하면 된다는 장점이 있다.  
(손님이 음식을 주문할때는 일일이 요리 방법을 지시할 필요 없이 메뉴명 알려주면 되고, 주방에서는 정해진 절차에 따라 음식을 조리하는데, 이상한 음식이 나오면 손님의 말이 아니라 주방을 조사하면 되는 것과 같다.)

두 줄 요약
1. props, 상태 끌어올리기를 생각할 필요 없이 데이터를 가져오고 변경하는 건 store만 통하면 된다.
2. 로직 분리에 좋다. 상태 가공 로직에 에러가 생기면 redux 코드쪽만 디버깅하면 된다.

## 2. 주요 개념
Redux의 핵심 개념은 다음 네가지다.  
store, action, dispatch(), reducer

<img src="https://ko.redux.js.org/assets/images/ReduxDataFlowDiagram-49fa8c3968371d9ef6f2a1486bd40a26.gif" alt="redux data flow diagram" width="500" />

이게 다 뭐꼬? 음식점 예시로 알아보자.

*손님이 음식을 주문할때는 일일이 요리 방법을 지시할 필요 없이 메뉴명(Action)으로만 주문하면(dispatch) 되고, 주방(store)에서는 요리사(reducer)가 정해진 절차에 따라 음식을 조리한다.*

- action: 주문서
- dispatch(): 주문하기
- reducer: 요리사
- store: 주방

이걸 머릿속에 넣고 음식점 시뮬레이션을 돌려보자.

난 주방에서 햄버거 좀 만들어줬으면 좋겠다.

1. action(주문서)를 보낸다. action에는 햄버거 만드는 행동과 그 때 사용했으면 하는 부가 데이터를 같이 적는다.
    ```js
    {
      type: ACTION_MAKE_HAMBUGER,
      payload: {
        patty: 'beef',
        sauce: 'mustard',
        vegetable: 'lectus'
      }
    }
    ```

2. 햄버거 만들어달라는 action을 적은 주문서를 주방(store)에 보낸다. (dispatch)

3. 주문서를 받으면 메뉴명(action.type)에 해당하는 행동(로직)을 수행한다. 여기서는 햄버거 만들어달라고 했으므로 요리사(reducer)가 알고 있는 햄버거 만들기 행동(`빵 + patty + sauce + vegetable`)대로 해서 햄버거를 주방(store)에 저장한다.
  
4. 손님이 햄버거를 달라고 요청한다. (store에게 데이터 달라고 요청한다)

## 3. 실제 코드로 알아보기
배우는 단계니까 action, reducer를 분리하지 않고 한 파일 안에 다 때려넣는 코드를 작성해보자.

store에 counter 정보를 갖고 있고, 버튼을 누르면 state가 증가 혹은 감소하는 카운터 앱을 만들 것이다.

### reducer 작성
src/reducers 폴더 아래에 index.js를 생성한다. 여기에 reducer를 작성할 것이다.

이 counter reducer는 action에 해당되는 행동을 한 뒤에 counter에 결과를 저장한다.
```js
// src/reducers/index.js
const counter = (state = 0, action) => {
  switch(action.type) {
    case 'INCREMENT':
      return state + 1;
    case 'DECREMENT':
      return state - 1;
    default:
      break;
  }
};

export default counter;
```

### store 생성
앱의 최상위인 index.js에서 store를 갖고 있어야 하므로 여기에 createStore()로 store를 생성한다.

```js
// index.js
import { createStore } from 'redux';

const store = createStore(rootReducer);

const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement
);

// root.render() 부분을 함수로 감싸준 다음,
const render = () => root.render(
    <App />
);
render();

// store가 render 함수를 구독하도록 한다. store에 변화가 생기면 render를 실행한다.
store.subscribe(render);
```
`store.subscribe(render)` 덕분에 store에 변화가 생기면 화면이 갱신된다.

### 값 변경 버튼 만들기
store.getStat()로 store에 저장된 값들을 가져오고, store.dispatch()에 액션을 전달해서 값을 변경한다.

```js
// index.js
const render = () => root.render(
    <App
      value={store.getState()}
      onIncrement={() => store.dispatch({ type: "INCREMENT" })}
      onDecrement={() => store.dispatch({ type: "DECREMENT" })}
    />
);
```

```js
// App.js
function App({ value, onIncrement, onDecrement }) {
  return (
    <div className="App">
      Clicked: {value} times
      <button onClick={onIncrement}>
        +
      </button>
      <button onClick={onDecrement}>
        -
      </button>
    </div>
  );
}

export default App;
```

## 4. combineReducers
카운터 예제에서는 reducer가 counter 하나여서 괜찮았는데 만약 todos, counter, ... 처럼 reducer가 여러개로 늘어난다면?
reducers 폴더의 index.js에서 combineReducers로 여러개의 리듀서들을 합쳐서 rootReducer로 묶어서 내보내면 된다.
```js
// src/reducers/index,js
import { combineReducers } from "redux";
import counter from "./counter";
import todos from "./todos";

const rootReducer = combineReducers({
  todos,
  counter
});

export default rootReducer;
```

## 5. Provider
```js
// index.js
const render = () => root.render(
    <App
      value={store.getState()}
      onIncrement={() => store.dispatch({ type: "INCREMENT" })}
      onDecrement={() => store.dispatch({ type: "DECREMENT" })}
    />
);
```
위의 index.js 예제를 보면 기껏 store에 데이터를 저장해도 App에 props로 일일이 데이터를 내려주고 있다. App의 하위 컴포넌트들이 store를 사용하려면 props으로 받아야 한다. 어디서든 접근하기 위해 store를 만든 목적에 어긋난다. 그렇다면 바로 접근하기 위해서는 어떻게 해야 할까? react-redux의 Provider 기능을 이용하자. React에서 제공하는 Context 기능을 이용하기 위해 provider 혹은 useContext를 사용하는 것과 비슷하다.

`npm install react-redux`로 react-redux를 설치한다.  
`<Provider>`로 App을 둘러싸서 그 곳에 store를 저장할 것이다. 그러면 어디서든 useSelector()를 통해 store에 바로 접근할 수 있다.

```js
// index.js
const render = () => root.render(
  <Provider store={store}>
    <App
      onIncrement={() => store.dispatch({ type: "INCREMENT" })}
      onDecrement={() => store.dispatch({ type: "DECREMENT" })}
    />
  </Provider>
);
```

```js
// App.js
import { useSelelctor } from 'react-redux';

function App({ onIncrement, onDecrement }) {
  // state는 store에 저장된 reducer들의 집합이다.
  const counter = useSelector((state) => state.counter);

  return (
    <div className="App">
      Clicked: {counter} times
      <button onClick={onIncrement}>
        +
      </button>
      <button onClick={onDecrement}>
        -
      </button>
    </div>
  );
}

export default App;
```