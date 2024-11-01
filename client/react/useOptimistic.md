# useOptimistic

`useOptimistic`는 비동기 작업 동안에도 사용자가 즉시 반응을 느낄 수 있도록, 실제 작업이 완료되기 전 UI 상태를 미리 업데이트할 수 있게 돕는 React Hook입니다.\
예를 들어, 데이터가 서버로 전송되는 동안 화면에서는 마치 데이터가 이미 반영된 것처럼 보일 수 있습니다.\
이를 “낙관적 업데이트”라고 부르며, 예상 결과를 미리 보여주는 방식입니다.

데이터베이스에서 처리되기도 전에 상태 등을 즉시 업데이트하는 것이 '낙관적 업데이트'라는 일종의 개념이며 패턴입니다.

## 사용법 요약

```jsx
const updateFn = (currentState, optimisticValue) => {
  // 낙관적 상태를 업데이트하여 반환
}

// useOptimistic 사용 예시
const [optimisticState, addOptimisticValue] = useOptimistic(
  initialState,
  updateFn // 낙관적 업데이트를 위한 함수
);
```

`useOptimistic`는 초기 상태 `initialState`와 업데이트 함수 `updateFn`을 받아, 낙관적인 상태인 `optimisticState`와 새로운 값을 추가하는 함수 `addOptimisticValue`를 반환합니다.

### 매개변수 설명

- `state`: 작업이 진행되지 않을 때의 기본 상태입니다.

- `updateFn(currentState, optimisticValue)`: 현재 상태 `currentState`와 낙관적 값 `optimisticValue`를 인수로 받아 두 값을 합쳐 새 상태를 반환하는 함수입니다.
  - `currentState`: 현재 유지되고 있는 상태 값입니다.
  - `optimisticValue`: `addOptimisticValue`로 추가한 새 값으로, 예상되는 상태 변화 내용을 담고 있습니다. 이 값은 `currentState`와 결합되어 최종적으로 업데이트된 상태를 구성합니다.

### 반환값

- `optimisticState`: 현재 상태 `currentState`와 낙관적 업데이트에 기반한 예상 상태입니다. 작업이 진행 중이지 않다면 `initialState`와 동일한 값을 갖지만, 작업이 진행 중이라면 `updateFn`을 통해 계산된 새 상태를 나타냅니다.

- `addOptimisticValue`: 새로운 예상 값을 추가하고 싶을 때 사용하는 함수로, `optimisticValue`라는 인수를 받아 `updateFn`을 호출합니다.

### 폼 업데이트 예시

`useOptimistic`는 비동기 작업이 진행되는 동안 화면에서 예상 결과를 미리 표시할 수 있게 도와줍니다.\
예를 들어, 메시지를 서버에 전송하는 동안에도 UI에는 이미 메시지가 반영된 것처럼 보일 수 있도록 하는 것입니다.\
다음 예시는 사용자가 메시지를 작성하고 "전송" 버튼을 눌렀을 때, 메시지가 서버에 전달되기 전이라도 화면에 바로 반영되는 방법을 보여줍니다.

#### 전체 코드 예시

1. 상태 설정\
초기 메시지 목록을 `useState`로 관리합니다.\
`messages`는 현재 메시지 목록을 담고 있으며, 서버 응답을 받을 때 이 값을 업데이트합니다.

2. 낙관적 상태 관리\
`useOptimistic`를 사용해 낙관적 메시지 목록(`optimisticMessages`)을 만듭니다.\
`addOptimisticMessage` 함수를 사용해 예상 메시지를 UI에 즉시 반영할 수 있습니다.

3. 폼 제출 로직\
`formAction` 함수는 폼 데이터를 처리하고, 낙관적인 메시지를 추가한 후 실제 `sendMessage` 함수로 비동기 전송을 수행합니다.

4. 컴포넌트 구성\
각 메시지는 화면에 즉시 표시되며, 전송 중일 때는 "Sending..."이라는 텍스트가 추가됩니다.

코드 예시

```jsx
// App.js
import { useOptimistic, useState, useRef } from "react";
import { deliverMessage } from "./actions.js";

function Thread({ messages, sendMessage }) {
  // 폼의 참조를 설정해 초기화에 사용
  const formRef = useRef();

  // 폼 제출 시 실행되는 함수
  async function formAction(formData) {
    // 폼 데이터에서 "message" 필드의 값을 가져와 낙관적으로 추가
    addOptimisticMessage(formData.get("message"));
    // 폼을 초기화해 사용자가 새 메시지를 입력할 수 있도록 함
    formRef.current.reset();
    // 비동기 메시지 전송
    await sendMessage(formData);
  }

  // 낙관적 메시지 상태 설정
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    // 현재 상태와 새 메시지를 결합해 낙관적 메시지 상태를 반환
    (state, newMessage) => [
      ...state,
      { text: newMessage, sending: true } // 새 메시지에 "전송 중" 표시 추가
    ]
  );

  return (
    <>
      {optimisticMessages.map((message, index) => (
        <div key={index}>
          {message.text}
          {!!message.sending && <small> (Sending...)</small>}
        </div>
      ))}
      <form action={formAction} ref={formRef}>
        <input type="text" name="message" placeholder="Hello!" />
        <button type="submit">Send</button>
      </form>
    </>
  );
}

export default function App() {
  // 초기 메시지 상태 설정
  const [messages, setMessages] = useState([
    { text: "Hello there!", sending: false }
  ]);

  // 메시지 전송 함수
  async function sendMessage(formData) {
    // 1초의 지연을 통해 비동기 작업을 시뮬레이션
    const sentMessage = await deliverMessage(formData.get("message"));
    // 서버 응답을 받은 후, 메시지 목록을 업데이트
    setMessages((messages) => [...messages, { text: sentMessage }]);
  }

  return <Thread messages={messages} sendMessage={sendMessage} />;
}
```

```jsx
// actions.js
export async function deliverMessage(message) {
  await new Promise((res) => setTimeout(res, 1000)); // 1초 지연
  return message;
}
```

#### 보조 함수 예시

`deliverMessage` 함수는 실제 서버에 메시지를 전송하는 대신, 1초의 지연 후 메시지를 반환하는 가짜 함수로 비동기 작업을 모방합니다.

```jsx
코드 복사
// actions.js
export async function deliverMessage(message) {
  await new Promise((res) => setTimeout(res, 1000)); // 1초 지연
  return message;
}
```

#### 코드 설명

- `useOptimistic`을 통해 `optimisticMessages`와 `addOptimisticMessage`를 생성했습니다.\
`addOptimisticMessage는` 사용자가 메시지를 전송 버튼을 누르면 `formAction` 함수에서 호출되어 `optimisticMessages`에 즉시 반영됩니다.

- 사용자는 "전송 중" 텍스트와 함께 메시지를 바로 볼 수 있으며, 서버에서 실제 응답이 도착하면 최종적으로 messages 상태가 업데이트됩니다.

이렇게 `useOptimistic`을 활용해 사용자가 실시간 반응을 느끼게 하면서도, 서버 응답에 따라 최종 상태를 업데이트하는 방식으로 UI를 설계할 수 있습니다.
