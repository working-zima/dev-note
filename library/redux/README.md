---
description: 자바스크립트 앱을 위한 예측 가능한 상태 컨테이너인 Redux에 관한 정보입니다.
---

# Redux

리덕스를 사용하는 이유는 Flux 아키텍처에서 파생된 구조와, 단방향 데이터 흐름(One-way data flow)의 이점을 활용하여 애플리케이션의 상태 관리를 더 명확하고 예측 가능하게 만들기 위함입니다.

## Flux 패턴

Facebook에서 제안한 애플리케이션 아키텍처 패턴입니다.

### Flux 패턴 구조

Flux 패턴에서 데이터는 항상 한 방향으로 흐르며, 상태 변경은 직접 조작하지 않고 오직 액션을 통해서만 일어납니다.\
상태 변경은 오직 액션을 통해서만 일어나므로, 예측 가능한 흐름이 보장됩니다.

```plain
[View] → (Action) → [Dispatcher] → [Store] → [View]
```

- Action: 사용자 상호작용이나 이벤트에 의해 발생하는 의도를 담은 객체입니다.

- Dispatcher: 모든 액션을 받아서 Store에 **전달**하는 중앙 허브입니다.

- Store: 애플리케이션의 상태를 저장하고, 변경된 상태를 구독자에게 알려줍니다.

- View: React 컴포넌트처럼 UI를 렌더링하며, Store의 상태를 바탕으로 동작합니다.

## Redux는 Flux의 단순화 버전

- Dispatcher 제거: 리덕스에서는 중앙 dispatcher 대신, `dispatch()` 함수로 액션을 직접 보냅니다.

- Store는 하나만 존재: Redux는 단일 중앙 Store를 사용하여 전체 앱 상태를 관리합니다.

- Reducer 함수 사용: 상태 변경 로직은 `reducer` 함수로 명확하게 분리됩니다.

  ![redux-simple-flow](./img/redux-simple-flow.png)

## redux 설치

### 공식 권장 툴킷 설치

redux Core만도 내부에 포함되어 따로 설치 안 해도 됩니다.\
`createSlice`, `configureStore`, `createAsyncThunk` 등 편의 기능 포함, Redux를 쉽게 쓰기 위한 표준 도구(설정, 유틸, 미들웨어 등)도 함께 설치합니다.

React와 연결이 안 되기 때문에 단독으로는 `useSelector`, `useDispatch`, `Provider`를 사용하지 못합니다.

```bash
npm install @reduxjs/toolkit
```

### React에서 Redux를 쓸 때의 공식 표준 설치

React에서 Redux를 쓸 때의 공식 표준입니다.\
react-redux는 React에서 `Provider`, `useSelector`, `useDispatch` 등 연결 기능 제공합니다.

```bash
npm install @reduxjs/toolkit react-redux
```

### 오리지널 Redux Core만 설치

가장 낮은 수준의 Redux 기능만 포함(`createStore`, `combineReducers`, `dispatch`, `subscribe` 등)하며 React와 연결하는 기능 없습니다.

모든 설정(미들웨어, 비동기 처리 등)을 수동으로 해야 하기 때문에 요즘은 거의 사용하지 않습니다.

```bash
npm install redux
```

## redux 예시

### reducer

입력 파라미터로 Old State, Dispatched Action을 받고 New State Object를 반환합니다.\
따라서 동일한 입력값을 넣으면 항상 정확히 같은 출력이 산출되어야 하는 순수한 함수로 사용해야합니다.

## 참고

- [Why REDUX ? and basic principals of REDUX](https://medium.com/@nipunsachintha/why-redux-and-basic-principals-of-redux-e99905e808a2)
- [Redux Flow](https://rainbowcd.tistory.com/65)
