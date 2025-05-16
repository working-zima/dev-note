# React Redux

## React Redux 모달 관리 기본 예시

```plain
src/
└── app
    ├── components
    │   ├── Modal.jsx
    │   └── OpenModalButton.jsx
    │
    ├── reducer
    │   ├── modal.js
    │   └── index.js
    │
    ├── store
    │   └── index.js
    │
    ├── App.js
    └── index.js
```

### store/index.js

Redux `store`를 생성하고, `reducer`를 연결하며 상태 구독을 설정합니다.

`store.subscribe()`는 상태가 변경될 때마다 실행되는 함수로, UI를 자동 리렌더링하지 않기 때문에
보통 디버깅, 콘솔 출력, 외부 저장소 동기화 등에 사용됩니다.

```js
import { createStore } from "redux";

import { rootReducer } from "../reducer"; // 모든 리듀서를 합친 루트 리듀서

const store = createStore(rootReducer); // 스토어 생성 (애플리케이션 상태 저장소)

store.subscribe(() => {
  console.log(store.getState()); // 상태 변경 시마다 현재 상태를 콘솔에 출력
});

export default store;
```

### reducer/index.js

여러 개의 `reducer`를 하나의 `rootReducer`로 병합하여 `state.modal.isShow`로 접근 가능하게 만듭니다.

```js
import { combineReducers } from "redux";

import modalReducer from "./modal";

const rootReducer = combineReducers({
  modal: modalReducer, // 모달 관련 리듀서를 modal 상태로 등록
});

export default rootReducer;
```

### reducer/modal.js

액션의 타입에 따라 상태를 업데이트합니다.

```js
export const TOGGLE_MODAL = "TOGGLE_MODAL";
export const OPEN_MODAL = "TOGGLE_MODAL";
export const CLOSE_MODAL = "TOGGLE_MODAL";

const initialState = {
  isShow: false,
};

/**
 * 모달 리듀서
 * @param {*} state : 현재 상태
 * @param {*} action : 액션 객체
 * @returns
 */
const modalReducer = (state = initialState, action) => {
  switch (action.type) {
    case TOGGLE_MODAL:
      return { ...state, isShow: !state.modal.isShow };
    case OPEN_MODAL:
      return { ...state, isShow: true  };
    case CLOSE_MODAL:
      return { ...state, isShow: false };
    default:
      return state;
  }
};

export modalReducer;
```

### index.js

하위 컴포넌트들이 `useSelector`, `useDispatch` 사용 가능하도록 Redux의 `Provider`를 사용해 앱 전체에 store를 연결합니다.

```js
import React from "react";
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

### components/OpenModalButton.jsx

리듀서가 이 액션을 처리해 모달이 열리도록 합니다.

```js
import { useDispatch } from "react-redux";

import { OPEN_MODAL } from "../reducer";

export default function OpenModalButton() {
  const dispatch = useDispatch(); // 액션을 store로 보내기 위한 함수

  const openModal = () => {
    dispatch({ type: OPEN_MODAL }); // 모달 열기 액션 디스패치
  };

  return <button onClick={openModal}>모달 열기</button>;
}
```

### components/Modal.jsx

`useSelector`로 상태값(isShow)을 가져와 모달 표시 여부 판단합니다.\
`useSelector()`는 이전 값과 새로운 값을 얕은 비교로 판단해서가 자동으로 리렌더링됩니다.

`useDispatch`로 닫기 버튼 클릭 시 모달을 닫는 액션 디스패치합니다.

```js
import { useDispatch, useSelector } from "react-redux";

import { CLOSE_MODAL } from "../reducer";

export default function OpenModalButton() {
  const isShow = useSelector((state) => state.modal.isShow); // 상태 읽기
  const dispatch = useDispatch();

  const closeModal = () => {
    dispatch({ type: CLOSE_MODAL }); // 모달 닫기 액션
  };

  if (!isShow) return null;

  return (
    <div className="modal">
      <div className="container">
        <div className="header">
          <h1>제목</h1>
          <button onClick={closeModal}>닫기</button>
        </div>
        <p>내용</p>
      </div>
    </div>
  );
}
```

### App.jsx

```js
import Modal from "./components/Modal";
import OpenModalButton from "./components/OpenModalButton";

function App() {
  return (
    <div className="App">
      <OpenModalButton />
      <Modal />
    </div>
  );
}

export default App;
```

## Redux Toolkit
