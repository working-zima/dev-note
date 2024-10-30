# useActionState

`useActionState`는 formAction의 결과를 기반으로 state를 업데이트할 수 있도록 제공하는 Hook 입니다.

```jsx
import { useActionState } from "react";

const [state, formAction, isPending] = useActionState(fn, initialState, permalink?);
```

리엑트 버전에 따라 `useFormState`으로 사용해야 할 수 있습니다.

```jsx
import { useFormState } from 'react-dom';
```

## 레퍼런스

`useActionState(action, initialState, permalink?)`

컴포넌트 최상위 레벨에서 `useActionState`를 호출하여 폼 액션이 실행될 때 업데이트되는 컴포넌트 state를 생성합니다.\
`useActionState`는 기존의 formAction 함수와 초기 state를 전달받고, 폼에서 사용할 새로운 액션을 반환합니다. 이와 함께 최신 폼 sta te와 액션이 여전히 진행(Pending) 중인지 여부도 반환합니다. 최신 폼 State는 제공된 함수에도 전달됩니다.

```jsx
import { useActionState } from "react";

async function increment(previousState, formData) {
  return previousState + 1;
}

function StatefulForm({}) {
  const [state, formAction] = useActionState(increment, 0);
  return (
    <form>
      {state}
      <button formAction={formAction}>Increment</button>
    </form>
  )
}
```

폼 state는 폼을 마지막으로 제출했을 때 액션에서 반환되는 값입니다. 폼이 제출되기 전이라면 전달한 초기 state와 같습니다.

Server Action과 함께 사용하는 경우, `useActionState`를 사용하여 hydration이 완료되기 전에도 폼 제출에 대한 서버의 응답을 보여줄 수 있습니다.

### 매개변수

- `fn`: 폼이 제출되거나 버튼을 눌렀을 때 호출될 함수입니다. 함수가 실행될 때, 첫 번째 인수로 폼의 이전 state를 전달합니다. state는 초기에 전달한 `initialState`이고, 이후에는 이전 실행의 반환값입니다. 그 후 일반적으로 폼 액션에 전달하는 인수들이 이어집니다.

- `initialState`: 초기 state로 설정하고자 하는 값으로, 직렬화 할 수 있는 값일 수 있습니다. 액션이 처음 호출된 후에는 이 인수를 무시합니다.

- optional `permalink`: 이 폼이 수정하는 고유의 URL이 포함된 문자열입니다. 점진적인 향상과 함께 동적 콘텐츠(예: 피드)가 있는 페이지에서 사용합니다. `fn`이 서버 액션이고 폼이 자바스크립트 번들이 로드되기 전에 제출된다면, 브라우저는 현재의 페이지 URL이 아닌 명시된 `permalink의` URL로 이동합니다. React가 상태를 전달하는 방법을 알 수 있도록 동일한 폼 컴포넌트가 대상 페이지에 렌더링 되어야 합니다(동일한 액션 `fn` 및 `permalink` 포함). 폼이 hydrated하면 이 매개변수는 아무런 영향을 미치지 않습니다.

### 반환값

`useActionState`는 다음 3가지 값들이 포함된 배열을 반환합니다.

- 현재 state입니다. 첫 번째 렌더링에서는 전달한 `initialState`와 일치합니다. 액션이 실행된 이후에는 액션에서 반환한 값과 일치합니다.

- form 컴포넌트의 `action` prop에 전달하거나 폼 내부 `button` 컴포넌트의 `formAction` prop에 전달할 수 있는 새로운 액션입니다.

- 대기 중인 전환(Pending Transition)이 있는지 여부를 알려주는 `isPending` 플래그입니다.

### 주의 사항

- React 서버 컴포넌트를 지원하는 프레임워크와 함께 사용할 때, `useActionState`는 클라이언트에서 자바스크립트가 실행되기 이전에도 폼을 상호작용하도록 만들 수 있습니다. 서버 컴포넌트를 사용하지 않는다면 컴포넌트 지역 state와 동일합니다.

- `useActionState`에 전달한 함수는 첫 번째 인수로 이전 혹은 초기 state를 추가로 받습니다. 이를 통해 `useActionState`를 사용하지 않고 직접 폼 액션을 사용했을 경우와는 다른 시그니처를 가지게 됩니다.

## 사용법

### 폼 액션에서 반환된 정보 사용하기

컴포넌트의 최상위 레벨에서 `useActionState`를 호출하면 폼이 마지막으로 제출된 시점에 액션이 반환한 값에 접근할 수 있습니다.

```jsx
import { useActionState } from 'react';
import { action } from './actions.js';

function MyComponent() {
  // useActionState(action prop, initial state)
  const [state, formAction] = useActionState(action, null);
  // ...
  return (
    <formAction={formAction}>
      {/*...*/}
    </form>
  );
}
```

useActionState는 다음 3가지 항목들이 포함된 배열을 반환합니다.

1. `state`: 폼의 현재 `state`입니다.\
처음에는 전달한 `initial state`로 설정되며, 폼이 제출된 후에는 전달한 `action prop`의 반환값으로 설정됩니다.

2. `formAction`: `<form>`의 `action prop`에 전달할 새로운 액션(`formAction`)입니다.

3. `state`: 액션이 처리되는 동안 사용할 수 있는 `대기(Pending) state`입니다.

폼을 제출하면 전달한 액션 함수가 호출됩니다. 액션의 반환값은 폼의 새로운 현재 `state`가 됩니다.

전달한 `action`은 또한 폼의 현재 `state`를 새로운 첫 번째 인수로 받게 됩니다. 폼이 처음 제출되면 제공한 `initial state`이며, 이후 제출에서는 액션이 마지막으로 호출된 시점의 반환값이 됩니다. 나머지 인수는 `useActionState`를 사용하지 않았을 때와 동일합니다.

```jsx
function action(currentState, formData) {
  // ...
  return 'next state';
}
```

### 폼 제출 후 정보 표시하기

#### 오류 표시하기

예시 1 of 2: 오류 표시하기
Server Action에서 반환한 오류 메시지나 토스트와 같은 메시지를 표시하려면 해당 액션을 useActionState 호출로 감싸세요.

```jsx
//App.js
actions.js

import { useActionState, useState } from "react";
import { addToCart } from "./actions.js";

function AddToCartForm({itemID, itemTitle}) {
  const [message, formAction, isPending] = useActionState(addToCart, null);
  return (
    <formAction={formAction}>
      <h2>{itemTitle}</h2>
      <input type="hidden" name="itemID" value={itemID} />
      <button type="submit">Add to Cart</button>
      {isPending ? "Loading..." : message}
    </form>
  );
}

export default function App() {
  return (
    <>
      <AddToCartForm itemID="1" itemTitle="JavaScript: The Definitive Guide" />
      <AddToCartForm itemID="2" itemTitle="JavaScript: The Good Parts" />
    </>
  )
}
```

```jsx
"use server";

export async function addToCart(prevState, queryData) {
  const itemID = queryData.get('itemID');
  if (itemID === "1") {
    return "Added to cart";
  } else {
    // Add a fake delay to make waiting noticeable.
    await new Promise(resolve => {
      setTimeout(resolve, 2000);
    });
    return "Couldn't add to cart: the item is sold out.";
  }
}
```

#### 폼 제출 후 구조화된 정보 표시하기

예시 2 of 2: 폼 제출 후 구조화된 정보 표시하기

```jsx
import { useActionState, useState } from "react";
import { addToCart } from "./actions.js";

function AddToCartForm({itemID, itemTitle}) {
  const [formState, formAction] = useActionState(addToCart, {});
  return (
    <formAction={formAction}>
      <h2>{itemTitle}</h2>
      <input type="hidden" name="itemID" value={itemID} />
      <button type="submit">Add to Cart</button>
      {formState?.success &&
        <div className="toast">
          Added to cart! Your cart now has {formState.cartSize} items.
        </div>
      }
      {formState?.success === false &&
        <div className="error">
          Failed to add to cart: {formState.message}
        </div>
      }
    </form>
  );
}

export default function App() {
  return (
    <>
      <AddToCartForm itemID="1" itemTitle="JavaScript: The Definitive Guide" />
      <AddToCartForm itemID="2" itemTitle="JavaScript: The Good Parts" />
    </>
  )
}
```

```jsx
"use server";

export async function addToCart(prevState, queryData) {
  const itemID = queryData.get('itemID');
  if (itemID === "1") {
    return {
      success: true,
      cartSize: 12,
    };
  } else {
    return {
      success: false,
      message: "The item is sold out.",
    };
  }
}
```

### 문제 해결

#### 액션이 더 이상 제출된 폼 데이터를 읽을 수 없습니다

액션을 `useActionState`로 감싸면 첫 번째 인수를 추가로 전달받습니다. 따라서 제출된 폼 데이터는 보통의 경우처럼 첫 번째로 전달되는 대신 두 번째 인수가 됩니다. 새롭게 추가된 첫 번째 인수는 폼의 현재 state입니다.

```jsx
function action(currentState, formData) {
  // ...
}
```

## 중요

React Canary 버전에서 `useActionState` 라고 불리는 이 API 는 React DOM 에 소속 되어있습니다.
