# useAtom

`useAtom` 훅은 상태에서 `atom`을 읽는 데 사용됩니다.\
상태는 `atom configs`과 `atom values`의 `WeakMap`으로 볼 수 있습니다.

`useAtom` 훅은 `atom` 값과 업데이트 함수(update function)를 튜플 형식으로 반환합니다. 이는 React의 `useState`처럼 동작합니다.\
이 훅은 `atom()`을 사용해 생성된 `atom` 구성을 매개변수로 받습니다.

`atom` 구성(atom config)을 생성할 때는 이에 연결된 값이 없습니다.\
`useAtom`을 통해 `atom`가 처음 사용될 때 초기 값이 상태에 저장됩니다.\
만약 `atom`가 파생된 `atom`(derived atom)라면, 읽기 함수(read function)가 호출되어 초기 값을 계산합니다.\
`atom`가 더 이상 사용되지 않으면, 즉 해당 `atom`를 사용하는 모든 컴포넌트가 언마운트되고 `atom` 구성이 더 이상 존재하지 않으면 상태의 값은 가비지 컬렉션됩니다.

```tsx
const [value, setValue] = useAtom(anAtom);
```

`setValue`는 하나의 인자만 받으며, 이 값은 `atom`의 쓰기 함수(write function)의 세 번째 매개변수로 전달됩니다.\
최종 결과는 쓰기 함수가 어떻게 구현되었는지에 따라 달라집니다.\
만약 쓰기 함수가 명시적으로 설정되지 않았다면, `atom`는 `setValue`에 전달된 값만 받게 됩니다.

**참고:** `atom` 섹션에서 언급했듯이, `atom`를 만들 때 참조 동일성(referential equality)이 중요하므로 이를 적절하게 처리해야 무한 루프(infinite loop)를 방지할 수 있습니다.

```tsx
const stableAtom = atom(0);
const Component = () => {
  const [atomValue] = useAtom(atom(0)); // 매 렌더링마다 `atom` 인스턴스가 새로 생성되기 때문에 무한 루프가 발생
  const [atomValue] = useAtom(stableAtom); // 이 코드는 괜찮음
  const [derivedAtomValue] = useAtom(
    useMemo(
      // 이 코드도 괜찮음
      () => atom((get) => get(stableAtom) * 2),
      []
    )
  );
};
```

**참고:** React는 컴포넌트를 호출할 책임이 있으므로 컴포넌트는 idempotent 을 가져야 하며 여러 번 호출될 수 있어야 합니다.\
종종 `props`나 `atom`가 변경되지 않았더라도 추가적인 리렌더가 발생할 수 있습니다.\
커밋 없이 추가적인 리렌더가 발생하는 것은 예상되는 동작으로, 이는 React 18에서 `useReducer`의 기본 동작입니다.

## 시그니처(Signatures)

```tsx
// 원시형 또는 쓰기 가능한 파생 `atom`
function useAtom<Value, Update>(
  atom: WritableAtom<Value, Update>,
  options?: { store?: Store }
): [Value, SetAtom<Update>];

// 읽기 전용 `atom`
function useAtom<Value>(
  atom: Atom<Value>,
  options?: { store?: Store }
): [Value, never];
```

## `atom` 종속성(How atom dependency works)

"읽기(read)" 함수가 호출될 때마다 종속성(dependencies)과 종속된 `atom`(dependents)가 새로 고쳐집니다.

`읽기 함수(read function)`는 `atom`의 첫 번째 매개변수입니다.\
만약 B가 A에 의존하면, A는 B의 종속성이고 B는 A의 종속된 `atom`가 됩니다.

```tsx
const uppercaseAtom = atom((get) => get(textAtom).toUpperCase());
```

`atom`를 생성할 때 종속성은 아직 존재하지 않습니다.\
첫 번째 사용 시 읽기 함수가 실행되어 `uppercaseAtom`이 `textAtom`에 의존한다는 결론이 나옵니다.\
따라서 `uppercaseAtom`은 `textAtom`의 종속`atom`로 추가됩니다.\
이후 `uppercaseAtom`의 읽기 함수가 다시 실행되면(`textAtom`이 업데이트 되었기 때문에) 종속성은 다시 생성되며, 이전의 종속`atom`는 새롭게 갱신됩니다.

## `atom`는 필요할 때 생성 가능(Atoms can be created on demand)

여기서 소개된 기본 예제들에서는 `atom`를 컴포넌트 밖에서 전역적으로 정의하지만, `atom`는 언제든지 생성할 수 있습니다. `atom`는 객체 참조 동일성(object referential identity)에 의해 식별되기 때문에, 필요할 때 언제든지 생성 가능합니다.

렌더 함수(render functions) 내에서 `atom`를 생성할 경우, `useRef`나 `useMemo`와 같은 훅을 사용해 메모이제이션(memoization)을 하는 것이 좋습니다.\
그렇지 않으면 컴포넌트가 렌더링될 때마다 `atom`가 재생성될 것입니다.

`atom`는 `useState`나 다른 `atom` 안에 저장할 수도 있습니다.\
예를 들어, 이슈 #5에서 확인해 보세요.

`atom`를 전역적으로 캐시할 수도 있습니다.\
이 예제나 저 예제를 참조하세요.

`atomFamily`를 사용하면 파라미터화된 `atom`(parameterized atoms)를 만들 수 있습니다.

## useAtomValue

```tsx
const countAtom = atom(0);

const Counter = () => {
  const setCount = useSetAtom(countAtom);
  const count = useAtomValue(countAtom);

  return (
    <>
      <div>count: {count}</div>
      <button onClick={() => setCount(count + 1)}>+1</button>
    </>
  );
};
```

`useSetAtom` 훅과 유사하게, `useAtomValue`는 읽기 전용 `atom`에 접근할 수 있게 해줍니다.\
또한 읽기-쓰기 가능한 `atom`의 값에도 접근할 수 있습니다.

## useSetAtom

```tsx
const switchAtom = atom(false);

const SetTrueButton = () => {
  const setCount = useSetAtom(switchAtom);
  const setTrue = () => setCount(true);

  return (
    <div>
      <button onClick={setTrue}>Set True</button>
    </div>
  );
};

const SetFalseButton = () => {
  const setCount = useSetAtom(switchAtom);
  const setFalse = () => setCount(false);

  return (
    <div>
      <button onClick={setFalse}>Set False</button>
    </div>
  );
};

export default function App() {
  const state = useAtomValue(switchAtom);

  return (
    <div>
      State: <b>{state.toString()}</b>
      <SetTrueButton />
      <SetFalseButton />
    </div>
  );
}
```

이 코드는 `atom`의 값을 읽지 않고 업데이트할 필요가 있을 때 `useSetAtom()`을 사용할 수 있는 예입니다.

성능이 중요한 경우, `const [, setValue] = useAtom(valueAtom)`를 사용하면 `valueAtom` 업데이트 시 불필요한 리렌더가 발생할 수 있기 때문에 `useSetAtom()`을 사용하여 값만 업데이트할 수 있습니다.
