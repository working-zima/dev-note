# React에서 제어 컴포넌트와 비제어 컴포넌트

## form 요소에 대한 제어 컴포넌트

React가 Form의 입력값을 제어하는 관리주체가 됩니다.\
사용자가 값을 입력할 때마다 데이터를 갱신하며 값을 입력할 때마다 리렌더링이 발생합니다.
즉, 입력을 하였을 때 즉시 유효성 검사가 가능합니다.

### 제어 컴포넌트 예시

```jsx
import { useState } from 'react';

export default function App() {
  const [name, setName] = useState('');

  return (
    <form>
      <label>name : </label>
      <input
        value={name}
        onChange={(e) => {
          setName(e.target.value);
          }
        }
      />
    </form>
  );
}
```

## form 요소에 대한 비제어 컴포넌트

React가 Form의 입력값을 제어하지 않고 DOM이 관리주체가 됩니다.\
특정 시점 DOM에서 Pull 하여 데이터를 갱신하며 값을 입력할 때 리렌더링이 발생하지 않습니다.
즉, submit을 하였을 때만 유효성 검사가 가능합니다.

### 비제어 컴포넌트 예시

```jsx
import { useRef } from 'react';

export default function App() {
  const nameRef = useRef(null);

  const submitName = (e) => {
    e.preventDefault();

    console.lot(nameRef.current.value);
  }

  return (
    <form>
      <label>name : </label>
      <input
        ref={nameRef}
        placeholder="이름을 입력하세요."
        }
      />
    </form>
  );
}
```

## 참고 자료

- [[10분 테코톡] 세인의 제어 컴포넌트와 비제어 컴포넌트](https://www.youtube.com/watch?v=PBgQKK6nelo)
