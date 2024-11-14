# Provider

`Provider` 컴포넌트는 컴포넌트 서브트리에 상태를 제공하는 역할을 합니다. 여러 개의 `Provider`를 사용하여 여러 서브트리에 상태를 제공할 수 있으며, 이들은 중첩될 수도 있습니다. 이는 React Context와 비슷하게 동작합니다.

만약 원자가 `Provider` 없이 트리에서 사용된다면, 기본 상태가 사용됩니다. 이 상태를 "provider-less mode"라고 부릅니다.

`Provider`는 세 가지 이유로 유용합니다:

1. 각 서브 트리에 다른 상태를 제공하기 위해.
2. 원자의 초기 값을 설정하기 위해.
3. 원자들을 모두 초기화하기 위해 다시 마운트할 때.

```tsx
const SubTree = () => (
  <Provider>
    <Child />
  </Provider>
);
```

## Signatures

```tsx
const Provider: React.FC<{
  store?: Store;
}>;
```

원자 설정은 값을 보유하지 않습니다. 원자 값은 별도의 스토어에 존재합니다. `Provider`는 스토어를 포함하고 컴포넌트 트리 아래에서 원자 값을 제공하는 역할을 합니다. `Provider`는 React Context의 제공자와 비슷하게 동작합니다. `Provider`를 사용하지 않으면 기본 스토어로 동작하는 provider-less 모드가 적용됩니다. 만약 서로 다른 컴포넌트 트리에서 서로 다른 원자 값을 유지해야 한다면 `Provider`가 필요합니다. `Provider`는 선택적으로 `store`라는 prop을 받을 수 있습니다.

```tsx
const Root = () => (
  <Provider>
    <App />
  </Provider>
);
```

## store prop

`Provider`는 선택적으로 `store`라는 prop을 받아서 해당 subtree에 제공할 수 있습니다.

### 예시

```tsx
const myStore = createStore();

const Root = () => (
  <Provider store={myStore}>
    <App />
  </Provider>
);
```

## useStore

이 훅은 컴포넌트 트리 내에서 스토어를 반환합니다.

```tsx
const Component = () => {
  const store = useStore();
  // ...
};
```
