# useFormStatus

`useFormStatus`는 폼이 제출될 때의 상태 정보를 쉽게 확인하게 해주는 Hook입니다.\
예를 들어, 폼이 제출 중일 때 버튼을 비활성화하거나 상태에 따라 다른 메시지를 보여주는 데 유용합니다.

## 기본 사용법

이 Hook을 사용하면 `pending`, `data`, `method`, `action`이라는 네 가지 정보를 얻을 수 있습니다:

- `pending`: `true`일 때는 폼이 제출 중이라는 뜻이고, `false`일 때는 제출이 끝났음을 나타냅니다.

- `data`: 폼에서 보낸 데이터를 담고 있는 객체로, 현재 제출 중이 아닐 때는 `nul`l이 됩니다.

- `method`: 폼이 데이터를 전송할 때 사용할 메서드(예: `GET` 또는 `POST`)를 나타냅니다.

- `action`: 폼 제출 시 호출되는 함수를 가리키며, 이 함수가 지정되지 않았으면 `null`이 됩니다.

## 예시 코드

폼 내부에 `Submit` 버튼을 두고, 제출 중일 때는 비활성화하려는 경우 다음과 같이 작성할 수 있습니다:

```jsx
import { useFormStatus } from "react-dom";

function Submit() {
  const { pending } = useFormStatus();
  return <button disabled={pending}>Submit</button>;
}

export default function App() {
  return (
    <form action="/Submit">
      <Submit />
    </form>
  );
}
```

여기서 `pending`이 `true`일 때 버튼이 비활성화됩니다.

## 주의 사항

- 폼 내부에서만 작동: `useFormStatus`는 `<form>` 안에 있어야 작동하며, 폼과 같은 컴포넌트 내부에 있을 때 상태를 정확히 가져올 수 있습니다.

- 동일 컴포넌트 폼 상태 추적 불가: `useFormStatus`는 같은 컴포넌트에서 렌더링된 `<form>`의 상태는 추적하지 않습니다.
