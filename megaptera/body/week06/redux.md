# 3. Redux 따라하기

## 학습 키워드

- Redux
- Reflect

## Redux

### Provider

Redux의 store을 React와 연결하기 위해 사용\
컴포넌트 안에서 state를 사용할 수 있도록 울타리 역할을 함

```jsx
import ReactDOM from 'react-dom/client';
import { Provider } from "react-redux";

import App, { store } from "./App";

function main() {
  const container = document.getElementById('root');
  if (!container) {
    return;
  }

  const root = ReactDOM.createRoot(container);

  // App 컴포넌트 하위의 컴포넌트에게 state를 사용할 수 있도록 함
  root.render(
    <Provider store={ store }>
      <App />
    </Provider>
  );
}

main();
```

### useDispatch

store의 dispatch 메서드를 반환\
action을 dispatch 할 수 있음

```jsx
import { useDispatch } from 'react-redux';

export default function App() {
  const dispatch = useDispatch();

  function increase() {
    // dispatch(action) 형태
    dispatch({ type: 'increase' });
  }

  function decrease() {
    // dispatch(action) 형태
    dispatch({ type: 'decrease' })
  }
}
```

### useSelector

store의 state를 가져오고, state가 변할 때마다 업데이트\
함수를 인자로 넘겨서 return 되는 값이 state\
해당 state만 사용하고 있는 컴포넌트만 렌더링

```jsx
import { useSelector } from 'react-redux'

// 함수를 인자로 받음
const value = useSelector((state) => state.value);

return (
  <div>
    {value}
  </div>
)
```

### reducer

```jsx
// reducers.ts

function reducer(state = { value: 0 }, action) {
  switch (action.type) {
    case: "increase":
      return { ...state, value: state.value + 1};
    case: "decrease":
      return { ...state, value: state.value - 1};
    default:
      return state;
  }
}
```

### createStore

Redux 라이브러리에서 createStore 함수를 사용하여 Redux 스토어를 생성

```jsx
// Store.ts

import { createStore } from 'redux'
import reducer from './reducers'

const store = createStore(reducer);
```

## Redux Toolkit

### configureStore()

Redux Toolkit의 store을 생성

```tsx
import { configureStore } from "@reduxjs/toolkit";
import counterSlice from "./counterSlice";

export const store = configureStore({
  // 각 Slice의 reducer들의 집합
  reducer: {
    // counterSlice의 reducers의 return 모음
    counter: counterSlice.reducer,
  },
})
```

### createSlice()

slice 이름, 초기 state를 받음\
reducer 및 state에 해당하는 action creator과 action type을 자동으로 생성하는 함수\
작은 단위의 store를 만듦

```tsx
import { createSlice } from "@reduxjs/toolkit";

export default const counterSlice = createSlice({
  // Slice의 이름
  name: 'testCounter',
  // State의 초기값
  initialState: { value: 0 },
  // reducer들
  reducers: {
    // actionType이 increase인 reducer
    increase: (state, action) => {
      // action은 {type: 'Slice의 이름/actionType이', payload: 3}
      // {type: 'testCounter/increase', payload: 3} 형식으로 받음
      state.value += action.payload;
    },
    // actionType이 decrease인 reducer
    decrease: (state, action) => {
      state.value -= action.payload;
    },
  },
})
```

### useSelector()

slice의 state에서 값을 가져오고, state가 변할 때마다 업데이트
함수를 인자로 넘겨서 return 되는 값이 state

```tsx
import { useSelector } from "react-redux";

function Counter() {
  const count = useSelector((state) => state.counter.value);

  return (
    <div>
      {count}
    </div>
  )
}
```

### useDispatch()

dispatch 메서드를 반환, action을 dispatch 할 수 있음

```tsx
import { useDispatch } from "react-redux";

const dispatch = useDispatch();

function increase() {
  // slice변수명.actions.actionType(payload)
  dispatch(counterSlice.actions.increase(3));
}

function decrease() {
  dispatch(counterSlice.actions.decrease(3));
}
```

### createAction()

Redux action type과 creator를 도와주기 위한 헬퍼함수\
createAction을 이용해 action을 만들어 dispatch에 인자로 넣어주는 방법도 있음

```tsx
import { createAction } from "@reduxjs/tookit";
import { useDispatch } from "react-redux";

const dispatch = useDispatch();

const increaseAction = createAction("testCounter/increase")
const decreaseAction = createAction("testCounter/decrease")

function increase() {
  dispatch(increaseAction);
}

function decrease() {
  dispatch(decreaseAction);
}
```

## Reflect

Proxy와 같이 중간에서 가로챌 수 있는 JavaScript 작업에 대한 메서드를 제공하는 내장 객체입니다.\
함수 객체가 아니므로 생성자로 사용할 수 없습니다.

### Reflect.get()

Reflect.get() 정적 메서드는 객체의 속성을 가져오는 함수입니다.\
target[propertyKey]와 비슷합니다.

```jsx
Reflect.get(target, propertyKey[, receiver])
```

- target\
: 속성을 가져올 대상 객체.

- propertyKey\
: 가져올 속성의 이름.

- receiver Optional\
: 대상 속성이 접근자라면 this의 값으로 사용할 값.\
Proxy와 함께 사용하면, 대상을 상속하는 객체를 사용할 수 있습니다.

```jsx
const object1 = {
  x: 1,
  y: 2,
};

console.log(Reflect.get(object1, 'x'));
// Expected output: 1

const array1 = ['zero', 'one'];

console.log(Reflect.get(array1, 1));
// Expected output: "one"
```

## Redux 따라하기

### `src/stores/BaseStore.ts` 파일

```tsx
export type Action<Payload> = {
  type: string;
  payload?: Payload;
}

type Reducer<State, Payload> = (state: State, action: Action<Payload>) => State;

type Reducers<State> = Record<string, Reducer<State, any>>;

type Listener = () => void;

/**
 * singleton()이 필요 없는 것들을 모아둔 class\
 * State 타입은 Store에서 받아옴
 */
export default class BaseStore<State> {
  state: State;

  reducer: Reducer<State, any>;

  // 각 컴포넌트의 forceUpdate를 담을 set 객체
  listeners = new Set<Listener>();

  // Store에서 initialState와 reducer을 받아옴
  constructor(initialState: State, reducers: Reducers<State>) {
    this.state = initialState;

    /**
     * dispatch에서 state와 action을 전달\
     * action에 따라서 상태(state)를 변경하는 역할
     * @param state
     * @param action
     * @returns new state
     */
    this.reducer = (state: State, action: Action<any>): State => {
      const reducer = Reflect.get(reducers, action.type);
      if (!reducer) {
        return state;
      }
      return reducer(state, action);
    };
  }

  /**
   * action을 reducer에게 보내는 역할을 가진 메서드\
   * 호출시 강제 리렌더링
   * @param action
   */
  dispatch<T>(action: Action<T>) {
    this.state = this.reducer(this.state, action);
    this.listeners.forEach((listener) => listener());
  }

  addListener(listener: Listener) {
    this.listeners.add(listener);
  }

  removeListener(listener: Listener) {
    this.listeners.delete(listener);
  }
}
```

### `src/stores/Store.ts` 파일

```tsx
import { singleton } from 'tsyringe';

import BaseStore, { Action } from './BaseStore';

/**
 * 상태 그 자체
 */
const initialState = {
  count: 0,
  name: 'Tester',
};

export type State = typeof initialState;

/**
 * action creator, 유저가 dispatch로 엑션을 보낼 때 사용
 * @param step action
 * @returns type: 'increase', payload: step
 */
function increase(state: State, action: Action<number>) {
  return {
    ...state,
    count: state.count + (action.payload ?? 1),
  };
}

/**
 * action creator, 유저가 dispatch로 엑션을 보낼 때 사용
 * @param step action
 * @returns type: 'decrease', payload: step
 */
function decrease(state: State) {
  return {
    ...state,
    count: state.count - 1,
  };
}

/**
 * reducer 함수의 세부 코드를 가진 프로퍼티
 */
const reducers = {
  increase,
  decrease,
};


/**
 * super를 통해 BaseStore로 initialState와 reducer을 넘김
 */
@singleton()
export default class Store extends BaseStore<State> {
  constructor() {
    super(initialState, reducers);
  }
}
```

### `src/hooks/useDispatch.ts` 파일

```tsx
import { container } from 'tsyringe';

import { Action } from '../stores/BaseStore';

import Store from '../stores/Store';

/**
 * Store 클래스의 dispatch를 호출할 수 있는 훅
 * @returns dispatch에게 인자를 전달할 수 있는 콜백함수
 */
export default function useDispatch<Payload>() {
  const store = container.resolve(Store);
  return (action: Action<Payload>) => store.dispatch(action);
}
```

### `src/hooks/useSelector.ts` 파일

```tsx
import { container } from 'tsyringe';

import { useEffect, useState } from 'react';

import Store, { State } from '../stores/Store';

import useForceUpdate from './useForceUpdate';

type Selector<T> = (state: State) => T;

/**
 * state를 주는 훅\
 * 'store.state'까지의 경로를 'state'로 줄여줌
 * @param selector 비어 있는 콜백 함수((state) => state.count)를 전달하면
 * @returns 인자로 받은 콜백 함수의 인자에 원래 들어가야할 'store.state'를 'state'로 줄여줌
 */
export default function useSelector<T>(selector: Selector<T>): T {
  // 변수 store을 통해 class Store에 접근 가능
  const store = container.resolve(Store);

  const [state, setState] = useState<T>(selector(store.state));

// 호출하면 강제 리렌더링
  const forceUpdate = useForceUpdate();

  // 새로운 상태가 기존 상태와 다르면 리렌더링
  useEffect(() => {
    const update = () => {
      const newState = selector(store.state);
      // TODO: T가 object일 때 처리 필요함.
      if (newState !== state) {
        setState(newState);
        forceUpdate();
      }
    };

    store.addListener(update);
    return () => store.removeListener(update);
  }, [store, forceUpdate]);

  return state;
}
```

### Dispatch와 Selector 사용.

```tsx
const dispatch = useDispatch();
const count = useSelector((state) => state.count);

dispatch({ type: 'increase' });
dispatch({ type: 'decrease' });

dispatch({ type: 'increase', payload: 10 });
```

## Action을 메서드로 처리하기

`src/stores/ObjectStore.ts` 파일

```tsx
type Listener = () => void;

export default class ObjectStore {
  private listeners = new Set<Listener>();

  /**
   * 호출한 컴포넌트에 forceUpdate를 추가하는 메서드
   * @param listener forceUpdate
   */
  addListener(listener: Listener) {
    this.listeners.add(listener);
  }

  /**
   * 호출한 컴포넌트에 forceUpdate를 제거하는 메서드
   * @param listener forceUpdate
   */
  removeListener(listener: Listener) {
    this.listeners.delete(listener);
  }

  /**
   * 각 컴포넌트의 forceUpdate를 호출하는 메서드
   */
  protected publish() {
    this.listeners.forEach((listener) => listener());
  }
}
```

### `src/stores/CounterStore.ts` 파일

```tsx
import { singleton } from 'tsyringe';

import ObjectStore from './ObjectStore';

@singleton()
export default class CounterStore extends ObjectStore {
  count = 0;

  increase(step = 1) {
    this.count += step;
    this.publish();
  }

  decrease() {
    this.count -= 1;
    this.publish();
  }
}
```

### `src/hooks/useObjectStore.ts` 파일

```tsx
import { useEffect } from 'react';

import useForceUpdate from './useForceUpdate';

import ObjectStore from '../stores/ObjectStore';

export default function useObjectStore<T extends ObjectStore>(store: T) {
  const forceUpdate = useForceUpdate();

  useEffect(() => {
    store.addListener(forceUpdate);
    return () => store.removeListener(forceUpdate);
  }, [store]);

  return store;
}
```

### `src/hooks/useCounterStore.ts` 파일

```tsx
import { container } from 'tsyringe';

import useObjectStore from './useObjectStore';

import CounterStore from '../stores/CounterStore';

export default function useCounterStore() {
  const store = container.resolve(CounterStore);

  return useObjectStore(store);
}
```

## 참고 자료

- [[흑묘테크] React-redux, Redux-toolkit (권민영)](https://www.youtube.com/watch?v=1utqWBVGbnA)
