# React Redux & Redux Toolkit

## Redux Toolkit 사용 이유

이전에 `reducer`는 순수 함수이여야 하며 기존 `state`를 바꿀 수 없다고 했습니다.\
하지만 Redux toolkit과 `createSlice`와 같은 함수를 사용하면 바꾸려고 해도 기존 상태를 바꿀 수 없습니다.\
Redux toolkit는 내부적으로 `immer`라는 다른 패키지를 사용하는데 이런 코드를 감지하고 자동으로 원래 있는 상태를 복제합니다.

아래처럼 직접 수정하는 코드를 작성해도 immer가 이 코드를 감지하고 기존 `state`는 건드리지 않고, 복제본을 만들어서 바꿔치기 하여 불변성(immunity)을 유지 합니다.

```js
increment(state) {
  state.value++;
}
```

## createSlice

`initialState`(초기 상태), `reducers`(액션 + 리듀서), `actions`객체(자동으로 만들어 줌), `slice.reducer`(스토어에 연결할 리듀서)를 한 번에 생성해주는 함수입니다.

### createSlice 없는 기존 Redux 방식

```js
export const INCREMENT = "INCREMENT";
export const INCREASE = "INCREASE";

// initialState
const initialState = { value: 0, showCounter: true };

// reducers
const counterReducer = (state = initialState, action) => {
  switch (action.type) {
    case INCREMENT:
      return { ...state, value: state.value + 1 };
    case INCREASE:
      return { ...state, value: state.value + action.payload };
    default:
      return state;
  }
};

// name
const rootReducer = combineReducers({
  counter: counterReducer,
});
```

### createSlice 사용시

```js
const counterSlice = createSlice({
  name: "counter", // state.counter
  initialState: { value: 0, showCounter: true }, // state.counter.value, state.counter.showCounter
  reducers: {
    increment(state) {
      state.value++; // immer가 감싸서 불변성 유지
    },
    increase(state, action) {
      state.value += action.payload;
    },
  },
});
```

## configureStore

`createStore()`대신 사용하는 Redux Toolkit의 함수로, 여러 리듀서를 객체 형태로 합쳐 등록할 수 있으며, redux-thunk 미들웨어도 자동으로 포함되어 있습니다.

### configureStore 없는 기존 Redux 방식

```js
import { createStore, combineReducers, applyMiddleware } from "redux";
import thunk from "redux-thunk";

import { rootReducer } from "../reducer";

const store = createStore(rootReducer, applyMiddleware(thunk));
```

### configureStore 사용시

```js
import { configureStore } from "@reduxjs/toolkit";

import counterReducer from "./counter";
import authReducer from "./auth";

// 따로 applyMiddleware 등을 등록하지 않아도 됨
const store = configureStore({
  reducer: {
    counter: counterReducer,
    auth: authReducer,
  },
});
```

## Redux Toolkit 예시

```plain
src/
└── app
    ├── components
    │   └── Counter.jsx
    │
    ├── store
    │   ├── auth.js
    │   ├── counter.js
    │   └── index.js
    │
    ├── App.js
    └── index.js
```

### components/Counter.js

```js
import { useSelector, useDispatch } from "react-redux";

import classes from "./Counter.module.css";
import { counterActions } from "../store/counter";

const Counter = () => {
  const dispatch = useDispatch(); // 액션을 디스패치하기 위한 함수 생성

  // Redux 스토어에서 상태 읽기 (state.counter는 configureStore에서 정의한 키)
  const counter = useSelector((state) => state.counter.value);
  const show = useSelector((state) => state.counter.showCounter);

  const incrementHandler = () => {
    dispatch(counterActions.increment());
  };

  const increaseHandler = () => {
    dispatch(counterActions.increase(5)); // 기존에는 `dispatch({ type: INCREASE, payload: 5 });` 로 사용
  };

  const decrementHandler = () => {
    dispatch(counterActions.decrement());
  };

  const toggleCounterHandler = () => {
    dispatch(counterActions.toggleCounter());
  };

  return (
    <main className={classes.counter}>
      <h1>Redux Counter</h1>
      {show && <div className={classes.value}>{counter}</div>}
      <div>
        <button onClick={incrementHandler}>Increment</button>
        <button onClick={increaseHandler}>Increase by 5</button>
        <button onClick={decrementHandler}>Decrement</button>
      </div>
      <button onClick={toggleCounterHandler}>Toggle Counter</button>
    </main>
  );
};

export default Counter;
```

### store/counter.js

```js
import { createSlice } from "@reduxjs/toolkit";

const initialCounterState = { value: 0, showCounter: true };

const counterSlice = createSlice({
  name: "counter",
  initialState: initialCounterState,
  reducers: {
    increment(state) {
      state.value++; // 기존에는 `return { ...state, value: state.value + 1 };` 로 사용
    },
    decrement(state) {
      state.value--;
    },
    increase(state, action) {
      state.value = state.value + action.payload;
    },
    toggleCounter(state) {
      state.showCounter = !state.showCounter;
    },
  },
});

export const counterActions = counterSlice.actions;

export default counterSlice.reducer;
```

### store/index.js

```js
import { configureStore } from "@reduxjs/toolkit";

import counterReducer from "./counter";
import authReducer from "./auth";

const store = configureStore({
  reducer: { counter: counterReducer, auth: authReducer },
}); // 기존에는 createStore() 사용

export default store;
```

### index.js

```js
import ReactDOM from "react-dom/client";

import { Provider } from "react-redux";

import App from "./App";
import store from "./store";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <Provider store={store}>
    <App />
  </Provider>
);
```
