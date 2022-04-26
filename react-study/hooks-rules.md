# Hook의 규칙
## ✌Hook 규칙 두 가지
1. 최상위에서만 Hook을 호출해야 한다. 반복문, 조건문, 중첩된 함수 내에서 Hook 사용 X
2. React 함수 컴포넌트 내에서만 Hook을 호출해야 한다. 일반 JS 함수에서는 X

이 두가지 규칙을 강제하기 위해 eslint-plugin-react-hooks라는 linter plugin을 제공하고 있다. 이 플러그인은 기본적으로 create react app에 포함되어 있다.

## 두 가지 규칙을 지켜야 하는 이유
1. 최상위에서만 Hook을 호출해야 한다. 반복문, 조건문, 중첩된 함수 내에서 Hook 사용 X  
-> 컴포넌트가 렌더링 될 때마다 항상 동일한 순서로 Hook이 호출되는 것을 보장하기 위해.
- 한 컴포넌트 안에서 state hook이나 effect hook이 여러 번 사용되는 경우가 있다. React에서는 hook들을 호출되는 순서대로 LinkedList 안에 저장한다. 그래서 재렌더링이 일어날 때마다 특정 state가 어떤 useState와 연결되어 있는지 구별할 수 있다.
- 재렌더링이 일어날 때마다 useState와 useEffect도 다시 실행된다. 첫번째가 아닌 재렌더링으로 다시 useState를 실행하게 되면 기존의 state 변수를 읽어들이고, useEffect를 실행하면 effect를 다시 탑재하는 일을 한다.
  ```javascript
  // ------------
  // 첫 번째 렌더링
  // ------------
  useState('Mary')           // 1. 'Mary'라는 name state 변수를 선언
  useEffect(persistForm)     // 2. 폼 데이터를 저장하기 위한 effect를 추가
  useState('Poppins')        // 3. 'Poppins'라는 surname state 변수를 선언
  useEffect(updateTitle)     // 4. 제목을 업데이트하기 위한 effect를 추가

  // -------------
  // 두 번째 렌더링
  // -------------
  useState('Mary')           // 1. name state 변수를 읽는다. (인자는 무시된다)
  useEffect(persistForm)     // 2. 폼 데이터를 저장하기 위한 effect가 대체된다.
  useState('Poppins')        // 3. surname state 변수를 읽는다. (인자는 무시된다)
  useEffect(updateTitle)     // 4. 제목을 업데이트하기 위한 effect가 대체된다.

  // ...
  ```
- 그런데 만약 조건문이나 반복문 안에서 hook을 사용했다면 조건에 따라 특정 hook을 실행하지 않고 건너뛰어서 다음 hook을 실행할 수 있다. 그러면 순서가 밀리면서 React가 기억하고 있던 hook의 순서와 일치하지 않아 버그가 발생한다.

  ```javascript
  // 🔴 조건문에 Hook을 사용함으로써 첫 번째 규칙을 깼다
    if (name !== '') {
      useEffect(function persistForm() {
        localStorage.setItem('formData', name);
      });
    }
  ```

  첫번째 렌더링에서는 name이 빈 문자열이 아니라 성공적으로 동작했다 치자. 하지만 그 다음 렌더링에서 사용자가 form을 초기화했다면? 조건이 false가 되어 이 hook을 건너뛰게 되면서, hook들의 호출 순서가 한 칸씩 밀려 버그가 발생한다.

  ```javascript
  useState('Mary')           // 1. name state 변수를 읽는다. (인자는 무시된다)
  // useEffect(persistForm)  // 🔴 Hook을 건너뛰었다!
  useState('Poppins')        // 🔴 2 (원래 3). surname state 변수를 읽는 데 실패
  useEffect(updateTitle)     // 🔴 3 (원래 4). 제목을 업데이트하기 위한 effect가 대체되는 데 실패
  ```
  따라서 이를 방지하려면 hook 안에 조건문을 작성하면 된다.
  ```javascript
  useEffect(function persistForm() {
    if (name !== '') {
      localStorage.setItem('formData', name);
    }
  });
  ```

2. React 함수 컴포넌트 내에서만 Hook을 호출해야 한다. 일반 JS 함수에서는 X
- 상태 관련 로직을 소스코드에서 명확히 보이게 하기 위함이다.
  - ✅React 컴포넌트 안에서 hook을 호출하기
  - ✅로직을 함수로 분리하고 싶다면, Custom hook을 만들어 거기에서 hook을 호출하기