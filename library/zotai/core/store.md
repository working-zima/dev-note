# Store

## createStore

이 함수는 새로운 빈 스토어를 생성하는 데 사용됩니다. 이 스토어는 `Provider`에 전달하여 사용할 수 있습니다.

스토어에는 세 가지 메서드가 있습니다: `get`은 원자 값을 가져오는 데 사용되고, `set`은 원자 값을 설정하는 데 사용되며, `sub`는 원자 변경을 구독하는 데 사용됩니다.

```tsx
const myStore = createStore();

const countAtom = atom(0);
myStore.set(countAtom, 1);
const unsub = myStore.sub(countAtom, () => {
  console.log("countAtom value is changed to", myStore.get(countAtom));
});
// unsub()로 구독 취소

const Root = () => (
  <Provider store={myStore}>
    <App />
  </Provider>
);
```

## getDefaultStore

이 함수는 프로바이더가 없는 모드에서 사용되는 기본 스토어를 반환합니다.

```tsx
const defaultStore = getDefaultStore();
```
