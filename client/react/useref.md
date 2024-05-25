# useRef

## initialvalue 타입에 따른 3가지

### useRef(initialValue: T): MutableRefObject

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

<br/>

### useRef(initialValue: T | null): RefObject

```tsx
interface RefObject<T> {
    readonly current: T | null;
}
```

`initialValue`가 `null`인 경우입니다.\
리턴 타입은 `RefObject<T>` 로 ref 객체의 `.current` 프로퍼티 값을 직접 변경할 수 없습니다.

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

<br/>

### useRef<T = undefined>(): MutableRefObject<T | undefined>

제네릭 타입이 undefined 즉, 타입을 지정하지 않은 경우입니다.\
리턴 타입은 `MutableRefObject<T | undefined>` 가 됩니다.

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
