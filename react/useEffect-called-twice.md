# useEffect가 두 번 호출되는 현상
Node.js로 API를 만들고 react에서 프록시를 설정한 다음, axios로 데이터를 불러오는 일을 하고 있었는데 useEffect가 두 번 호출되는 현상이 계속 발생했다. 결론은 백엔드도, 프록시도, axios의 문제도 아닌 **React 코드**의 문제였다.

## 해결방법 1
`index.js`로 가서 StrictMode가 켜져있는지 확인하자.  
Strict 모드에서는 검사를 위해 컴포넌트를 두 번씩 렌더링한다.

```js
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```
`<React.StrictMode>` 태그를 삭제하면 된다.
```js
root.render(
    <App />
);
```

## 해결방법 2
StrictMode를 껐는데도 useEffect()가 두 번 발생한다면  
`useEffect(() => {})`에 아래와 같이 빈 배열을 파라미터로 넣자

```js
useEffect(() => {},[])
```

그냥 `useEffect(() => {})`만 하게 되면 state가 변경될 때마다 useEffect()도 다시 실행되기 때문에 그것을 방지하기 위해 빈 배열을 파라미터로 주면 **처음 렌더링될 때 딱 한번만** 실행된다.

### 조건부 effect 발생
React 공식 문서의 조건부 effect 발생 항목을 보면  
특정 값이 변경될 때만 effect를 실행하도록 할 수 있다.

effect의 기본 동작은 모든 렌더링을 완료한 후 effect를 발생하는 것입니다. 이와 같은 방법으로 의존성 중 하나가 변경된다면 effect는 항상 재생성됩니다.

그러나 이것은 이전 섹션의 구독 예시와 같이 일부 경우에는 과도한 작업일 수 있습니다. source props가 변경될 때에만 필요한 것이라면 매번 갱신할 때마다 새로운 구독을 생성할 필요는 없습니다.

이것을 수행하기 위해서는 useEffect에 두 번째 인자를 전달하세요. 이 인자는 effect가 종속되어 있는 값의 배열입니다. 이를 적용한 예는 아래와 같습니다.

useEffect(
  () => {
    const subscription = props.source.subscribe();
    return () => {
      subscription.unsubscribe();
    };
  },
  [props.source],
);

### Reference
- https://breathtaking-life.tistory.com/106
- https://ko.reactjs.org/docs/hooks-reference.html#conditionally-firing-an-effect