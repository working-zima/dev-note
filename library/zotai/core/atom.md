# atom

atom 함수는 atom 설정(config)을 생성하기 위한 함수입니다.\
atom 설정은 단지 정의일 뿐 값을 가지고 있지 않기 때문에 "atom 설정"이라고 부릅니다.\
문맥이 명확하다면 간단히 "atom"이라고 부를 수 있습니다.

atom 설정은 불변 객체이며, atom 설정 객체 자체는 값을 보유하지 않습니다.\
atom 값은 store에 존재합니다.

기본 atom(설정)을 생성하려면 초기값을 제공하기만 하면 됩니다.

```tsx
import { atom } from "jotai";

const priceAtom = atom(10);
const messageAtom = atom("hello");
const productAtom = atom({ id: 12, name: "good stuff" });
```

유도된 atom을 생성할 수도 있습니다. 세 가지 패턴이 있습니다:

유도된 atom을 생성하려면 읽기 함수와 선택적 쓰기 함수를 전달합니다.

```tsx
/* 읽기 전용 atom */
const readOnlyAtom = atom((get) => get(priceAtom) _ 2)

/* 쓰기 전용 atom */
const writeOnlyAtom = atom(
  null, // 첫 번째 인수로 `null`을 전달하는 것이 관례입니다.
  (get, set, update) => {
  // `update`는 이 atom을 업데이트할 때 받는 단일 값입니다.
  set(priceAtom, get(priceAtom) - update.discount)
  // 또는 두 번째 인수로 함수를 전달할 수 있습니다.
  set(priceAtom, (price) => price - update.discount)
  },
)

/* 읽기-쓰기 atom */
const readWriteAtom = atom(
  (get) => get(priceAtom) _ 2,
  (get, set, newPrice) => {
    set(priceAtom, newPrice / 2)
  },
)
```

읽기 함수에서 `get`은 atom 값을 읽기 위해 사용되며 반응형이며 의존성이 추적됩니다.\
쓰기 함수에서 `get`도 atom 값을 읽기 위해 사용되지만 추적되지 않으며 Jotai v1 API에서는 비동기 값을 읽을 수 없습니다.

쓰기 함수에서 `set`은 atom 값을 쓰기 위해 사용되며 대상 atom의 쓰기 함수를 호출합니다.

## render 함수에서 atom 생성 시 주의사항

atom 설정은 어디서든 생성할 수 있지만, 참조 동등성이 중요합니다.\
동적으로 생성할 수도 있으며, render 함수에서 atom을 생성할 때는 `useMemo`나 `useRef`를 사용하여 안정적인 참조를 가져야 합니다.\
`useMemo`를 사용하는 것이 일반적이며 그렇지 않으면 `useAtom`과 함께 무한 루프를 초래할 수 있습니다.

```tsx
const Component = ({ value }) => {
  const valueAtom = useMemo(() => atom({ value }), [value]);
  // ...
};
```

## 시그니처

```tsx
// 기본 atom:
function atom<Value>(initialValue: Value): PrimitiveAtom<Value>;

// 읽기 전용 atom:
function atom<Value>(read: (get: Getter) => Value): Atom<Value>;

// 읽기-쓰기 유도 atom:
function atom<Value, Args extends unknown[], Result>(
  read: (get: Getter) => Value,
  write: (get: Getter, set: Setter, ...args: Args) => Result
): WritableAtom<Value, Args, Result>;

// 쓰기 전용 유도 atom:
function atom<Value, Args extends unknown[], Result>(
  read: Value,
  write: (get: Getter, set: Setter, ...args: Args) => Result
): WritableAtom<Value, Args, Result>;
```

`initialValue`: atom이 처음 생성될 때 반환할 초기값입니다. 이 값은 atom의 값이 변경될 때까지 유지됩니다.

`read`: atom이 읽힐 때마다 실행되는 함수입니다.\
`read`의 시그니처는 `(get) => Value`이며, `get`은 atom 설정을 가져와 Provider에 저장된 해당 atom 값을 반환하는 함수입니다.\
이때 의존성 추적이 이루어지므로 `get`이 특정 atom을 한 번이라도 참조하면, 해당 atom 값이 변경될 때마다 `read`가 다시 평가됩니다.

`write`: 주로 atom의 값을 수정하기 위해 사용되는 함수로, `useAtom`의 반환 값 중 두 번째 값인 `useAtom()[1]`을 호출할 때 실행됩니다.\
기본 atom에서 이 함수는 atom의 값을 변경하는 역할을 합니다.\
`write`의 시그니처는 `(get, set, ...args) => Result`입니다. `get`은 위에서 설명한 것과 유사하게 atom 값을 읽지만 의존성 추적은 하지 않습니다.\
`set`은 atom 설정과 새 값을 받아 atom의 값을 Provider에 업데이트하는 함수입니다.\
`...args`는 `useAtom()[1]` 호출 시 전달되는 인수입니다.\
`Result`는 `write` 함수의 반환값을 나타냅니다.

```tsx
const primitiveAtom = atom(initialValue); // 기본 atom 생성
const derivedAtomWithRead = atom(read); // 읽기 전용 유도 atom 생성
const derivedAtomWithReadWrite = atom(read, write); // 읽기-쓰기 유도 atom 생성
const derivedAtomWithWriteOnly = atom(null, write); // 쓰기 전용 유도 atom 생성
```

atom에는 두 가지 종류가 있습니다.\
하나는 쓰기 가능한 atom이고 다른 하나는 읽기 전용 atom입니다.\
기본 atom은 항상 쓰기 가능하며, 유도 atom의 경우 `write`가 정의된 경우에만 쓰기가 가능합니다.\
기본 atom의 `write`는 React의 `useState`의 `setState`와 동일한 역할을 합니다.

## debugLabel 속성

생성된 atom 설정은 선택적 `debugLabel` 속성을 가질 수 있습니다.\
debug 레이블은 디버깅 시 atom을 표시하는 데 사용됩니다.\
debug 레이블은 고유할 필요는 없지만, 구별 가능하도록 만드는 것이 좋습니다.

## onMount 속성

생성된 atom 설정은 선택적 `onMount` 속성을 가질 수 있습니다.\
`onMount`는 `setAtom` 함수를 인자로 받고 선택적으로 `onUnmount` 함수를 반환합니다.\
`onMount` 함수는 atom이 Provider에 처음으로 구독될 때 호출되며, `onUnmount`는 더 이상 구독되지 않을 때 호출됩니다.

```tsx
const anAtom = atom(1)
anAtom.onMount = (setAtom) => {
console.log('atom is mounted in provider')
setAtom(c => c + 1)
return () => { ... } // onUnmount 함수는 선택적입니다.
}

const Component = () => {
  useAtom(anAtom);
  useAtomValue(anAtom);

  useSetAtom(anAtom);
  useAtomCallback(useCallback((get) => get(anAtom), []));
  // ...
};
```

`setAtom` 함수 호출 시 atom의 쓰기가 호출됩니다.\
쓰기 커스터마이징을 통해 동작을 변경할 수 있습니다.

```tsx
const countAtom = atom(1);
const derivedAtom = atom(
  (get) => get(countAtom),
  (get, set, action) => {
    if (action.type === "init") {
      set(countAtom, 10);
    } else if (action.type === "inc") {
      set(countAtom, (c) => c + 1);
    }
  }
);
derivedAtom.onMount = (setAtom) => {
  setAtom({ type: "init" });
};
```

## 고급 API

Jotai v2부터 읽기 함수는 두 번째 인수로 `options`를 가집니다.\
`options.signal`을 사용하여 비동기 함수를 중단할 수 있으며, 새 계산이 시작되기 전에 중단됩니다.

```tsx
const readOnlyDerivedAtom = atom(async (get, { signal }) => {
  // signal을 사용하여 함수 중단
});

const writableDerivedAtom = atom(
  async (get, { signal }) => {
    // signal을 사용하여 함수 중단
  },
  (get, set, arg) => {
    // ...
  }
);
```

`signal` 값은 `AbortSignal`입니다.\
`signal.aborted`의 불리언 값을 확인하거나, `addEventListener`를 사용하여 `abort` 이벤트를 감지할 수 있습니다.\
`fetch` 사용 시에는 `signal`을 간단히 전달할 수 있습니다.\
아래는 `fetch` 사용 예시입니다.

```tsx
import { Suspense } from "react";
import { atom, useAtom } from "jotai";

const userIdAtom = atom(1);
const userAtom = atom(async (get, { signal }) => {
  const userId = get(userIdAtom);
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/users/${userId}?_delay=2000`,
    { signal }
  );
  return response.json();
});

const Controls = () => {
  const [userId, setUserId] = useAtom(userIdAtom);
  return (
    <div>
      User Id: {userId}
      <button onClick={() => setUserId((c) => c - 1)}>Prev</button>
      <button onClick={() => setUserId((c) => c + 1)}>Next</button>
    </div>
  );
};

const UserName = () => {
  const [user] = useAtom(userAtom);
  return <div>User name: {user.name}</div>;
};

const App = () => (
  <>
    <Controls />
    <Suspense fallback="Loading...">
      <UserName />
    </Suspense>
  </>
);

export default App;
```
