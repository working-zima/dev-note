# 3. Redux 따라하기

## 학습 키워드

- Redux
- Reflect

## Redux

### Provider

Redux의 store을 React와 연결하기 위해 사용\
컴포넌트 안에서 state를 사용할 수 있도록 울타리 역할을 함

```jsx
// main.tsx

import ReactDOM from 'react-dom/client';
import { Provider } from 'react-redux';

import store from './Store'
import App from './App';

function main() {
  const container = document.getElementById('root');
  if (!container) {
    return;
  }

  const root = ReactDOM.createRoot(container);

  root.render(
    // App 컴포넌트와 하위 컴포넌트에서 state를 사용하기 위해 Provider로 감싸줌
    <Provider store={store}>
      <App />
    </Provider>
  );
}

main();
```

### createStore

Redux 라이브러리에서 createStore 함수를 사용하여 Redux 스토어를 생성

```jsx
// Store.ts

import { createStore } from 'redux'
import reducer from './reducers'

const store = createStore(reducer);
```

### reducer

```jsx
// reducers.ts

function reducer(state, action) {
  return state
}
```

### useDispatch

store의 dispatch 메서드를 반환\
action을 dispatch 할 수 있음

```jsx
import { useDispatch } from 'react-redux';

export default function App() {
  const dispatch = useDispatch();

  function increase() {
    dispatch({ type: 'increase' });
  }

  function decrease() {
    dispatch({ type: 'decrease' })
  }
}
```

### useSelector

store의 state를 가져오고, state가 변할 때마다 업데이트\
함수를 인자로 넘겨서 return 되는 값이 state

```jsx
import { useSelector } from 'react-redux'

const value = useSelector((state) => state.value);

return (
  <div>
    {value}
  </div>
)
```

## Redux 따라하기

`src/stores/BaseStore.ts` 파일

```tsx
export type Action<Payload> = {
  type: string;
  payload?: Payload;
}

type Reducer<State, Payload> = (state: State, action: Action<Payload>) => State;

type Reducers<State> = Record<string, Reducer<State, any>>;

type Listener = () => void;

export default class BaseStore<State> {
  state: State;

  reducer: Reducer<State, any>;

  listeners = new Set<Listener>();

  constructor(initialState: State, reducers: Reducers<State>) {
    this.state = initialState;

    this.reducer = (state: State, action: Action<any>): State => {
      const reducer = Reflect.get(reducers, action.type);
      if (!reducer) {
        return state;
      }
      return reducer(state, action);
    };
  }

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

`src/stores/Store.ts` 파일

```tsx
import { singleton } from 'tsyringe';

import BaseStore, { Action } from './BaseStore';

const initialState = {
  count: 0,
  name: 'Tester',
};

export type State = typeof initialState;

function increase(state: State, action: Action<number>) {
  return {
    ...state,
    count: state.count + (action.payload ?? 1),
  };
}

function decrease(state: State) {
  return {
    ...state,
    count: state.count - 1,
  };
}

const reducers = {
  increase,
  decrease,
};

@singleton()
export default class Store extends BaseStore<State> {
  constructor() {
    super(initialState, reducers);
  }
}
```

`src/hooks/useDispatch.ts` 파일

```tsx
import { container } from 'tsyringe';

import { Action } from '../stores/BaseStore';

import Store from '../stores/Store';

export default function useDispatch<Payload>() {
  const store = container.resolve(Store);
  return (action: Action<Payload>) => store.dispatch(action);
}
```

`src/hooks/useSelector.ts` 파일

```tsx
import { container } from 'tsyringe';

import { useEffect, useState } from 'react';

import Store, { State } from '../stores/Store';

import useForceUpdate from './useForceUpdate';

type Selector<T> = (state: State) => T;

export default function useSelector<T>(selector: Selector<T>): T {
  const store = container.resolve(Store);

  const [state, setState] = useState<T>(selector(store.state));

  const forceUpdate = useForceUpdate();

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

Dispatch와 Selector 사용.

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

  addListener(listener: Listener) {
    this.listeners.add(listener);
  }

  removeListener(listener: Listener) {
    this.listeners.delete(listener);
  }

  protected publish() {
    this.listeners.forEach((listener) => listener());
  }
}
```

`src/stores/CounterStore.ts` 파일

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

`src/hooks/useObjectStore.ts` 파일

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

`src/hooks/useCounterStore.ts` 파일

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
