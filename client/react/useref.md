# useRef

`useRef`는 주로 두 가지 목적에 사용됩니다.

1. DOM 요소(HTML)에 직접 접근하고 조작할 때 사용
2. 리렌더링 되어도 초기화되지 않는 값을 유지할 때 사용

## Refs(참조)

리액트가 특별한 방식으로 제어하는 특별한 종류의 값입니다.\
리액트에서 `useRef` 훅을 import함으로 사용 가능합니다.

```tsx
import { useRef } from 'react';

const ref = useRef(initialValue);
```

### HTML 요소 연결 및 접근

모든 리액트 컴포넌트들에서 사용이 가능한 `ref` 속성으로 JSX 요소들과 연결할 수 있습니다.
`ref` 속성은 콜백 함수를 받고 컴포넌트가 마운트되거나 언마운트 된 이후에 즉시 실행됩니다.

`useRef`로부터 받는 참조 값들은 `current` 속성을 가지고 있습니다.

```tsx
import { useRef } from 'react';

const myRef = useRef()

<input
  ref={palyerName}
  type="text"
/>

console.log(palyerName.current) // <input type='text'>
console.log(palyerName.current.value) // input에 입력된 값
```

### 주의 (Refs로 DOM 제어)

일반적으로 리액트는 선언적인 코드를 쓴다는 개념이기 때문에 DOM을 직접 조작하는 게 아니라 리액트가 하게 해야 합니다.

#### 명령형 프로그래밍

`textRef.current`를 통해 DOM 요소의 스타일을 직접 변경합니다.
어떻게(How) 변경할지를 명시적으로 작성합니다.

```jsx
import { useRef } from 'react';

function App() {
  const textRef = useRef(null);

  const changeColor = () => {
    if (textRef.current) {
      textRef.current.style.color = 'red';
    }
  };

  return (
    <div>
      <div ref={textRef}>Hello, world!</div>
      <button onClick={changeColor}>Change Color</button>
    </div>
  );
}
```

#### 선언형 프로그래밍

`useRef`를 상태를 저장하거나 값의 변경을 추적하는 데 사용합니다.
무엇(What)을 원하는지 선언적으로 작성합니다.

```jsx
import { useState } from 'react';

function App() {
  const [color, setColor] = useState('black');

  return (
    <div>
      <div style={{ color }}>Hello, world!</div>
      <button onClick={() => setColor('red')}>Change Color</button>
    </div>
  );
}
```

#### 예시

아래 예시에서 `playerName.current.value = ''`은 DOM 요소를 직접 조작하고 있습니다.\
이렇게 명령적으로 사용하는 것은 좋지 않습니다.\
하지만 그저 `input`을 지우고 싶은 경우에 다른 상태 같은 것들과는 아무 연결이 되어 있지 않았으니 코드를 이런 식으로 작성해볼 수도 있습니다.\
참조를 사용하는 목적이 페이지의 모든 종류의 값들을 읽고 조정하기 위해서는 아니면서 코드를 많이 줄여 줄 수 있다면 사용해볼 수 있는 방법입니다.

```jsx
import { useRef, useState } from 'react';

export default function Player() {
  const playerName = useRef()

  const [enteredPlayerName, setEnteredPlayerName] = useState(null);

  function handleClick() {
    setEnteredPlayerName(playerName.current.value); // 값을 조작이 아닌 읽어들이는데 사용
    playerName.current.value = ''; // 명령적 사용
  }

  return (
    <section id="player">
      <h2>Welcome { enteredPlayerName ?? 'unknown entity'}</h2>
      <p>
        <input
          ref={playerName}
          type="text"
        />
        <button onClick={handleClick}>Set Name</button>
      </p>
    </section>
  );
}
```

### Refs VS. State

가장 큰 차이는 값이 변경될 때 재실행의 발생 유무 입니다.

위의 예시에서 state를 사용하지 않는다면 어떻게 될까요?

```jsx
import { useRef, useState } from 'react';

export default function Player() {
  const playerName = useRef()

  function handleClick() {
    playerName.current.value = ''; // 명령적 사용
  }

// state 대신 playerName.current.value 사용
  return (
    <section id="player">
      <h2>Welcome { playerName.current.value ?? 'unknown entity'}</h2>
      <p>
        <input
          ref={playerName}
          type="text"
        />
        <button onClick={handleClick}>Set Name</button>
      </p>
    </section>
  );
}
```

결과는 정의되지 않은 속성 값을 읽는 데 실패했다는 내용의 에러가 발생합니다.\
컴포넌트가 처음 렌더링될 때 `ref` 속성을 통한 연결이 수립되지 않습니다.\
첫 컴포넌트 렌더링 사이클에서 컴포넌트 함수가 처음 실행될 때 `playerName.current`는 정의되지 않는 것입니다.

또한 참조가 바뀔 때마다 컴포넌트 함수가 재실행되지 않기 때문에 그 다음에도 값을 볼 수 없습니다.

따라서 UI에 바로 반영되어야 하는 값들이 있을 때는 state를 사용해야 합니다.\
반대로 시스템 내부에 보이지 않는 쪽에서만 다루는 값들이나, UI에 직접적인 영향을 끼치지 않는 값들을 갖고 있을 경우 Refs를 사용할 수 있습니다.

### 리렌더링 되어도 초기화되지 않는 값을 유지

아래의 예시에서 `Start Challenge` 버튼을 클릭하면 `setTimeout` 이 작동합니다.
targetTime의 시간 이전에 `Stop Challenge` 버튼을 클릭하지 않으면 `You lost!`라는 문구가 나오게 됩니다.

```jsx
import { useRef, useState } from "react";

export default function TimerChallenge({title, targetTime}) {
  const timer = useRef()

  const [timerStarted, setTimerStarted] = useState(false);
  const [timerExpired, setTimerExpired] = useState(false)

  function handleStart() {
    timer.current = setTimeout(() => {
      setTimerExpired(true)
    }, targetTime * 1000);

    setTimerStarted(true);
  }

  function handleStop() {
    clearTimeout(timer.current);
  }

  return (
    <section className="challenge">
      <h2>{title}</h2>
      {timerExpired && <p>You lost!</p>}
      <p className="challenge-time">
        {targetTime} second{targetTime > 1 ? 's' : ''}
      </p>
      <p>
        <button onClick={timerStarted ? handleStop : handleStart}>
          {timerStarted ? 'Stop' : 'Start'} Challenge
        </button>
      </p>
      <p className={timerStarted ? 'active' : undefined}>
        {timerStarted ? 'Time is Running...' : 'Timer inactive'}
      </p>
    </section>
  )
}
```

#### 이때 `timer` 를 변수로 사용할 경우 문제

아래와 같은 순서가 되며 원하지 않는 결과를 볼 수 있습니다.

1. `setTimerStarted`에 의해 `timerStarted` 상태가 바뀜
2. `TimerChallenge` 컴포넌트가 재실행되고, 변수가 재생성 됨
3. `Stop Challenge` 버튼을 눌렀을 때는 `clearTimeout`이 가리키는 `timer` 는 이미 새로운 `timer` 이기 때문에 실행중인 `setTimeout`을 지우지 못함
4. `setTimerExpired(true)`이 `timerExpired`을 바꾸면서 실패

```jsx
import { useState } from "react";

export default function TimerChallenge({title, targetTime}) {
  let timer

  const [timerStarted, setTimerStarted] = useState(false);
  const [timerExpired, setTimerExpired] = useState(false)

  function handleStart() {
    timer = setTimeout(() => {
      setTimerExpired(true)
    }, targetTime * 1000);

    setTimerStarted(true);
  }

  function handleStop() {
    clearTimeout(timer);
  }

  return (
    <section className="challenge">
      <h2>{title}</h2>
      {timerExpired && <p>You lost!</p>}
      <p className="challenge-time">
        {targetTime} second{targetTime > 1 ? 's' : ''}
      </p>
      <p>
        <button onClick={timerStarted ? handleStop : handleStart}>
          {timerStarted ? 'Stop' : 'Start'} Challenge
        </button>
      </p>
      <p className={timerStarted ? 'active' : undefined}>
        {timerStarted ? 'Time is Running...' : 'Timer inactive'}
      </p>
    </section>
  )
}
```

#### `timer` 를 변수를 컴포넌트 밖에 사용하며 재생성을 막을 경우 문제

1. `setTimerStarted`에 의해 `timerStarted` 상태가 바뀜
2. `TimerChallenge` 컴포넌트가 재실행되어도 재생성 되지 않고, `clearTimeout` 으로 `setTimeout` 제거 가능

문제가 해결된 듯 보이지만, 만약 `TimerChallenge` 컴포넌트가 다수일 경우 문제가 발생합니다.
새로운 `TimerChallenge` 에서 `handleStart` 를 실행할 경우 `timer`는 앞에 발생한 문제와 같이 새로운 `TimerChallenge` 의 `setTimeout` 으로 초기화 되기 때문에 이전 `setTimeout` 을 지울 수 없습니다.

```jsx
import { useState } from "react";

let timer

export default function TimerChallenge({title, targetTime}) {
  const [timerStarted, setTimerStarted] = useState(false);
  const [timerExpired, setTimerExpired] = useState(false)

  function handleStart() {
    timer = setTimeout(() => {
      setTimerExpired(true)
    }, targetTime * 1000);

    setTimerStarted(true);
  }

  function handleStop() {
    clearTimeout(timer);
  }

  return (
    <section className="challenge">
      <h2>{title}</h2>
      {timerExpired && <p>You lost!</p>}
      <p className="challenge-time">
        {targetTime} second{targetTime > 1 ? 's' : ''}
      </p>
      <p>
        <button onClick={timerStarted ? handleStop : handleStart}>
          {timerStarted ? 'Stop' : 'Start'} Challenge
        </button>
      </p>
      <p className={timerStarted ? 'active' : undefined}>
        {timerStarted ? 'Time is Running...' : 'Timer inactive'}
      </p>
    </section>
  )
}
```

### forwardRef

부모 컴포넌트가 자식 컴포넌트의 노드에 접근하고자 할 때 사용합니다.\
부모에서 `ref`를 생성하고, 자식의 노드에 연결할 때 쓰는 것입니다.

```jsx
const SomeComponent = forwardRef((props, ref) => {})
```

`ref` (참조)는 `prop` (속성)이 아니기 때문에 다른 컴포넌트 또는 다른 컴포넌트의 요소에도 전달할 수 없습니다.\
하지만 `forwardRef`를 사용하면 참조를 컴포넌트에서 컴포넌트로 전달하여 다른 컴포넌트에서 사용될 수 있도록 합니다.

사용방법은 `forwardRef`가 `ref`를 전달받을 함수를 감싸도록 합니다.
`ref`는 두 번째 매개변수로 받습니다.

```jsx
import { useRef } from "react";

const dialog = useRef();

<FancyButton ref={ref}>Click me!</FancyButton>;
```

```jsx
import { forwardRef } from "react"

const FancyButton = forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));
```

#### 주의

부모 컴포넌트의 노드를 자식 컴포넌트에 전달할 때는 그냥 `props`로 전달하면 됩니다.\
부모에서 `ref` 를 생성하고, 부모의 노드에 연결한 다음, 연결된 결과를 자식에게 전달하는 것입니다.\
이 경우 `ref` 는 부모 컴포넌트에서 생성 및 연결까지 완료되었으므로 `forward` 해줄 이유가 없습니다.

### useImperativeHandle(ref, createHandle, dependencies?)

첫 번째 매개변수는 `forwardRef` 로 부터 받은 `ref` 입니다.\
두 번째 매개변수는 일반적으로 외부에서 사용할 수 있도록 노출하려는 속성과 메서드를 가진 객체(ref 핸들)를 반환하는 함수입니다.\
세 번째 매개변수틑 의존성입니다.\
`useEffect` 의 의존성을 생각하면 될거 같습니다.

```jsx
import { forwardRef, useImperativeHandle } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  useImperativeHandle(ref, () => {
    return {
      // ... 메서드를 여기에 입력하세요 ...
    };
  }, []);
  // ...
})
```

#### 사용법

부모 컴포넌트입니다.\
자식 컴포넌트인 `MyInput` 의 `input` 에 focus 효과를 주고 싶습니다.

```jsx
import { useRef } from 'react';
import MyInput from './MyInput.js';

export default function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.focus();
    // 이 작업은 DOM 노드가 노출되지 않으므로 작동하지 않습니다.
    // ref.current.style.opacity = 0.5;
  }

  return (
    <form>
      <MyInput placeholder="Enter your name" ref={ref} />
      <button type="button" onClick={handleClick}>
        Edit
      </button>
    </form>
  );
}
```

자식 컴포넌트입니다.\
`ref` 는 `prop` 이 아니기 때문에 전달할 수 없지만 `forwardRef` 를 사용하여 전달 받습니다.\
전달 받은 `ref` 를 `useImperativeHandle` 의 첫 번째 매개변수에 전달합니다.\
`useImperativeHandle` 의 두 번째 매개변수의 함수 객체 반환 값에 상위 컴포넌트에서 사용할 focus 메서드를 구현합니다.\
이때 `input` 을 조작하기 위한 `ref` 를 `input` 에 연결해줍니다.

```jsx
import { forwardRef, useRef, useImperativeHandle } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const inputRef = useRef(null); // input을 조작하기 위한 ref

  useImperativeHandle(ref, () => { // 상위 컴포넌트에 전달하기 위한 ref
    return {
      focus() {
        inputRef.current.focus();
      }
    };
  }, []);

  return <input {...props} ref={inputRef} />;
});

export default MyInput;
```

`<input>` DOM 노드를 노출하지 않고 `focus` 메서드만을 노출하게 되었습니다.

## initialvalue 타입에 따른 3가지

### `useRef(initialValue: T): MutableRefObject`

```tsx
interface MutableRefObject<T> {
    current: T;
}
```

제네릭 타입 T와 초깃값이 T로 일치하는 경우입니다.\
리턴 타입은 `MutableRefObject<T>` 가 됩니다.\
ref 객체의 `.current` 프로퍼티를 직접 변경할 수 있습니다.

```tsx
const localRef = useRef<number>(0); //MutableRefObject<number>

localRef.current += 1;
```

#### `MutableRefObject`

```tsx
import { useRef, MutableRefObject, useEffect } from 'react';

// MutableRefObject를 Props로 받는 자식 컴포넌트
type ChildProps = {
  countRef: MutableRefObject<number>;
};

const ChildComponent = ({ countRef }: ChildProps) => {
  useEffect(() => {
    console.log("Count from parent:", countRef.current);
  }, [countRef]);

  return <div>Check the console for count updates.</div>;
};

// 부모 컴포넌트
const ParentComponent = () => {
  const countRef = useRef(0);

  const handleClick = () => {
    countRef.current += 1;
  };

  return (
    <div>
      <ChildComponent countRef={countRef} />
      <button onClick={handleClick}>Increment Count</button>
    </div>
  );
};

export default ParentComponent;
```

<br/>

### `useRef(initialValue: T | null): RefObject`

```tsx
interface RefObject<T> {
    readonly current: T | null;
}
```

`initialValue`가 `null`인 경우입니다.\
반환 타입은 `RefObject<T>` 로 ref 객체의 `.current` 프로퍼티 값을 직접 변경할 수 없습니다.

```tsx
const inputRef = useRef<HTMLInputElement>(null); //RefObject<HTMLInputElement>

inputRef.current += 1; //Cannot assign to 'current' because it is a read-only property.
```

`current`의 하위 프로퍼티인 `.current.value`값은 수정 가능합니다.\
정의 상 `current` 프로퍼티만 `readonly`로, `current` 프로퍼티의 하위 프로퍼티인 `value`는 여전히 수정 가능합니다.

```tsx
const inputRef = useRef<HTMLInputElement>(null);

if (inputRef.current) {
  inputRef.current.value = "";
}

<input ref={inputRef} />
```

#### RefObject

반환 타입이 `RefObject<T>`이기 때문에 매개변수로 사용하는 경우 타입은 `RefObject<T>`으로 해야 합니다.

```tsx
import { useRef, RefObject } from 'react';

// inputRef를 매개변수로 사용하는 함수
const focusInput = (inputRef: RefObject<HTMLInputElement>) => {
  if (inputRef.current) {
    inputRef.current.focus();
  }
};

const App = () => {
  const inputRef = useRef<HTMLInputElement>(null);

  const handleClick = () => {
    focusInput(inputRef);
  };

  return (
    <div>
      <input ref={inputRef} type="text" placeholder="Click the button to focus me" />
      <button onClick={handleClick}>Focus the input</button>
    </div>
  );
};

export default App;
```

<br/>

### `useRef<T = undefined>(): MutableRefObject<T | undefined>`

제네릭 타입이 undefined 즉, 타입을 지정하지 않은 경우입니다.\
리턴 타입은 `MutableRefObject<T | undefined>` 가 됩니다.

#### `MutableRefObject<T | undefined>`

```tsx
import { useRef, useEffect } from 'react';

// MutableRefObject를 매개변수로 사용하는 함수
const usePrevious = <T,>(value: T): MutableRefObject<T | undefined> => {
  const ref = useRef<T>();
  useEffect(() => {
    ref.current = value;
  }, [value]);
  return ref;
};

const App = () => {
  const [count, setCount] = useState(0);
  const prevCountRef = usePrevious(count);

  const handleIncrement = () => {
    setCount(count + 1);
  };

  return (
    <div>
      <p>Current count: {count}</p>
      <p>Previous count: {prevCountRef.current}</p>
      <button onClick={handleIncrement}>Increment</button>
    </div>
  );
};

export default App;
```

<br/>

## useRef 사용 방법에 따른 2가지

### 로컬 변수 용도로 useRef를 사용하는 경우

`MutableRefObject<T>`를 사용해야 하므로 제네릭 타입과 같은 타입의 `initialValue`을 넣어줍니다.

### DOM을 직접 조작하기 위해 프로퍼티로 useRef 객체를 사용할 경우

`RefObject<T>`를 사용해야 하므로 `initialValue`로 `null`을 넣어줍니다.

<br/>

## 조작할 DOM을 배열로 관리하는 경우

`useRef(initialValue: T | null): RefObject`이기 때문에 배열에 들어갈 `button` 요소의 타입과 `null`의 타입을 사용합니다.

```tsx
const buttonsRef = useRef<(HTMLButtonElement | null)[]>([]);

{categories.map(
  (category, idx) => (
    <button
      key={category.id}
      ref={(el) => {
        buttonsRef.current[idx] = el
      }}
    >
      {category.name}
    </button>
  ))
}
```

<br/>

## 참고

- [useRef의 3가지 정의와 타입 알아보기 (feat. MutableRefObject, RefObject)](https://ch3coo2ca.github.io/2022-06-20/useref-types-in-typescript)
