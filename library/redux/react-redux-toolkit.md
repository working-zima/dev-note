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

const initialState = { value: 0, showCounter: true }; // initialState

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
```

### createSlice 사용시

```js
const counterSlice = createSlice({
  name: "counter", // Redux DevTools나 로그에서 구분이 잘 되도록 prefix로 붙는 값
  initialState: { value: 0, showCounter: true },
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

const rootReducer = combineReducers({
  counter: counterReducer, // state.counter
});

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
    counter: counterReducer, // state.counter
    auth: authReducer, // state.auth
  },
});
```

## Redux Toolkit 예시

```plain
src/
└── app/
    ├── components/
    │   └── Counter.jsx
    │
    ├── store/
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

## Redux에서 비동기 처리

Redux의 `reducer`는 반드시 순수 함수여야 합니다.\
즉, 외부에 의존하지 않고, 외부 상태를 변경하지 않으며, 동기적이며, 예측 가능한 출력을 가져야 합니다.

### 리듀서 내부 비동기 처리한 잘못된 예시

리듀서가 외부 요청에 의존하게 되면 동일한 입력에서도 결과가 달라지고, 상태 변경 흐름이 예측 불가능해지며 디버깅 및 테스트가 매우 어려워집니다.

```js
const reducer = (state, action) => {
  if (action.type === "FETCH_DATA") {
    fetch("/api/data") // ❌ 비동기 요청
      .then(...);
  }
};
```

### Thunk

RRedux에서는 비동기 로직을 리듀서 바깥으로 분리하기 위해 미들웨어를 사용합니다.\
그중 가장 널리 사용되는 미들웨어가 Redux Thunk입니다.

Thunk란, 액션 객체 대신 함수를 디스패치할 수 있게 해주는 구조입니다.
이 함수 안에서 비동기 작업을 자유롭게 수행하고, 그 결과에 따라 여러 개의 액션을 `dispatch()` 할 수 있습니다.

#### 컴포넌트에서 Thunk 사용 예시

```js
import { useEffect } from "react";
import { useDispatch, useSelector } from "react-redux";
import { fetchUsers } from "../store/user-slice";

export default function UserList() {
  const dispatch = useDispatch();
  const users = useSelector((state) => state.users.list);
  const status = useSelector((state) => state.users.status);
  const error = useSelector((state) => state.users.error);

  useEffect(() => {
    dispatch(fetchUsers()); // // Thunk 함수 디스패치
  }, [dispatch]);

  if (status === "loading") return <p>불러오는 중...</p>;
  if (status === "error") return <p style={{ color: "red" }}>에러: {error}</p>;

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>
          {user.name} ({user.email})
        </li>
      ))}
    </ul>
  );
}
```

#### Thunk 사용 예시

액션 대신 함수를 반환하고, 그 안에서 비동기 로직과 dispatch를 자유롭게 사용할 수 있습니다.

```js
// ./store/user-slice
import { createSlice } from "@reduxjs/toolkit";

// 기존 reducers
const userSlice = createSlice({
  name: "users",
  initialState: { list: [], status: null, error: null },
  reducers: {
    setUsers(state, action) {
      state.list = action.payload;
    },
    setStatus(state, action) {
      state.status = action.payload;
    },
    setError(state, action) {
      state.error = action.payload;
    },
  },
});

// 이처럼 액션 대신 Thunk 함수를 반환하고, 그 안에서 필요한 만큼 dispatch를 실행
export const fetchUsers = () => {
  return async (dispatch) => {
    dispatch(userActions.setStatus("loading"));

    try {
      const res = await fetch("https://jsonplaceholder.typicode.com/users");
      const data = await res.json();

      dispatch(userActions.setUsers(data));
      dispatch(userActions.setStatus("success"));
    } catch (err) {
      dispatch(userActions.setStatus("error"));
      dispatch(userActions.setError(err.message));
    }
  };
};

export const userActions = userSlice.actions;

export default userSlice.reducer;
```

### Redux Toolkit의 createAsyncThunk

Redux Toolkit에서는 위와 같은 비동기 흐름을 더 간결하고 구조적인 방식으로 처리할 수 있도록 `createAsyncThunk()` 유틸리티를 제공합니다.

`createAsyncThunk`는 비동기 작업을 위한 액션 생성기를 자동으로 만들어주고, 그에 따른 상태 변화 (`pending`, `fulfilled`, `rejected`)를 자동으로 관리할 수 있게 해줍니다.

#### createAsyncThunk 예시 - 사용자 컴포넌트

```js
import { useEffect } from "react";
import { useDispatch, useSelector } from "react-redux";
import { fetchUsers } from "../store/user-slice";

export default function UserList() {
  const dispatch = useDispatch();
  const users = useSelector((state) => state.users.list);
  const status = useSelector((state) => state.users.status);
  const error = useSelector((state) => state.users.error);

  useEffect(() => {
    dispatch(fetchUsers()); // createAsyncThunk 디스패치
  }, [dispatch]);

  if (status === "loading") return <p>불러오는 중...</p>;
  if (status === "error") return <p style={{ color: "red" }}>에러: {error}</p>;

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>
          {user.name} ({user.email})
        </li>
      ))}
    </ul>
  );
}
```

#### createAsyncThunk 예시 - 비동기 thunk 함수 정의 및 extraReducers 사용

```js
export const fetchUsers = createAsyncThunk(
  "fetchUsers", // (1) 액션 타입 prefix
  async (_, thunkAPI) => {
    const response = await fetch("https://jsonplaceholder.typicode.com/users");

    if (!response.ok) {
      return thunkAPI.rejectWithValue("서버 응답 실패"); // (2) 에러 발생 시 payload로 메시지 전달 가능
    }

    return await response.json();
  } // (3) payloadCreator
);

const userSlice = createSlice({
  name: "users",
  initialState: { list: [], status: "idle", error: null },
  reducers: {}, // 일반 액션은 여기에
  extraReducers: (builder) => {
    builder
      .addCase(fetchUsers.pending, (state) => {
        state.status = "loading";
      })
      .addCase(fetchUsers.fulfilled, (state, action) => {
        state.status = "success";
        state.list = action.payload;
      })
      .addCase(fetchUsers.rejected, (state, action) => {
        state.status = "error";
        state.error = action.payload || "알 수 없는 에러";
      });
  }, // (4) extraReducers 비동기 액션 처리
});
```

1. `"fetchUsers"`: 액션 prefix → `pending`, `fulfilled`, `rejected` 세 가지 자동 생성 (예: `"fetchUsers/pending"`)

2. `rejectWithValue(value)`: 에러 발생 시 `rejected` 액션에 추가 정보를 담을 수 있음

3. `(_, thunkAPI)`: 실제 비동기 로직을 수행하는 부분으로 비동기 요청의 두 번째 인자 → `dispatch`, `getState` 등 포함.

4. `extraReducers`: `createSlice()` 안에서 `createAsyncThunk`가 만들어낸 비동기 액션의 각 단계별 상태 업데이트 전용 공간

### 직접 작성한 Thunk와 createAsyncThunk 비교

edux의 `reducer`는 순수 함수이므로 비동기 로직은 외부에서 처리해야 합니다.\
이때 가장 기본적인 미들웨어가 Thunk이며, Redux Toolkit의 `createAsyncThunk`는 이를 구조화하고 자동화하여 더 유지보수하기 좋은 방식으로 만들어줍니다.\
특히 비동기 상태 흐름이 명확하게 드러나야 하는 규모 있는 애플리케이션에서는 `createAsyncThunk`가 권장됩니다.

| 항목                | 직접 작성한 Thunk           | createAsyncThunk                   |
| ------------------- | --------------------------- | ---------------------------------- |
| 액션 타입 자동 생성 | ❌ 수동                     | ✅ `prefix/pending` 등 자동 생성   |
| 상태 관리 통일성    | ❌ dispatch 분산            | ✅ slice 내부에 통합               |
| 에러 메시지 전달    | ❌ 직접 처리 필요           | ✅ `rejectWithValue()`로 처리 가능 |
| DevTools 추적       | ❌ 액션 이름 직접 지정 필요 | ✅ 명확한 이름으로 자동 추적 가능  |
