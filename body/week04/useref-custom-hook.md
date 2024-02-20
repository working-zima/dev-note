# 4. useRef & Custom Hook

## 학습 키워드

- useRef
- Hook의 규칙

## useRef

```jsx
const ref = useRef(initialValue);

console.log(ref.current);
// initialValue
```

컴포넌트의 생애주기 전체에 걸쳐서 유지되는 객체입니다.\
즉, 컴포넌트가 없어질 때까지 동일한 객체가 유지됩니다.\
객체 자체가 값은 아니고, 값을 참조하기 위한 객체입니다.\
즉, 언제든지 값을 변경할 수 있습니다.\

## state와의 차이

상태(state)가 변경되면 해당 컴포넌트와 하위 컴포넌트를 다시 렌더링하지만, 레퍼런스 객체의 현재 값(current)이 바뀌더라도 렌더링(화면)에 영향을 주지 않는다.

### 주요 용도

1. 컴포넌트가 사라질 때까지 동일한 값을 써야 하는 경우. ⇒ input 등의 ID 관리.
2. (특히 useEffect 등과 함께 쓰면서 만나게 되는) 비동기 상황에서 현재 값을 제대로 쓰고 싶은 경우.

Closure → 변수를 capture, bind를 깜빡하는 문제가 종종 일어남.

#### useRef를 사용하지 않은 경우

`filterText`는 `useEffect` 실행 당시의 값을 유지합니다.
클로저로 인해 이때의 `filterText`는 `useEffect` 실행 당시의 값으로 초기화된 값이 됩니다.

```jsx
const [filterText, setFilterText] = useState("");

useEffect(() => {
  setTimeout(() => {
    // 클로저 문제로 인해 비동기 작업 완료 후에도 초기 filterText 값인 ""을 출력함
    console.log("Inside setTimeout - Without useRef:", filterText);
  }, 3000);
}, []);

return (
  <div>
    <input
      type="text"
      placeholder="Type something"
      value={filterText}
      onChange={(e) => setFilterText(e.target.value)}
    />
  </div>
);
```

#### useRef를 사용한 경우

```jsx
const [filterText, setFilterText] = useState("");
const filterTextRef = useRef(filterText);

useEffect(() => {
  setTimeout(() => {
    // useRef를 사용하여 filterTextRef.current는 항상 최신 값을 유지함
    console.log("Inside setTimeout - With useRef:", filterTextRef.current);
  }, 3000);
}, []);

useEffect(() => {
  filterTextRef.current = filterText;
  // filterText 값이 변경될 때마다 filterTextRef.current에 최신 값 반영
}, [filterText]);

return (
  <div>
    <input
      type="text"
      placeholder="Type something"
      value={filterText}
      onChange={(e) => setFilterText(e.target.value)}
    />
  </div>
);
```

#### 왜 useRef인가?

위의 코드에서 `useRef`를 `useState`로 대체하면, 상태값의 변경으로 인해 컴포넌트가 리렌더링되기 때문에 클로저 문제를 해결할 수 없습니다.\
`useRef`를 사용하면 외부 범위의 변수를 참조하면서 렌더링에 영향을 주지 않고 값을 유지할 수 있기 때문에 클로저 문제를 해결할 수 있습니다.

## Custom Hook

로직을 재사용하기 위한 제일 쉬운 방법입니다.

평범하게 Extract Function을 수행하면 됩니다.\
컴포넌트가 대문자로 시작하는 PascalCase로 이름을 붙인다면, Hook은 “use”로 시작하는 camelCase로 이름을 붙이면 됩니다.

```tsx
// hooks/useFetchProduct.ts

export default function useFetchProducts() {
  const [products, setProducts] = useState<Product[]>([]);

  useEffect(() => {
    const fetchProducts = async () => {
      const url = "http://localhost:3000/products";
      const response = await fetch(url);
      const data = await response.json();
      setProducts(data.products);
    };

    fetchProducts();
  }, []);

  return products;
}
```

```tsx
// App.tsx

import useFetchProduct from "./hooks/useFetchProduct";

function App() {
  const products = useFetchProduct();
  return (
    <div>
      <TimerControl />
      <hr />
      <h1>상품</h1>
      <FilterableProductTable products={products} />
    </div>
  );
}

export default App;
```

컴포넌트 코드도 명확해지고, `setProducts`가 실수로 잘못 쓰일 문제까지 해소됩니다.

## Hook 규칙

Hook 호출은 규칙이 있어서 단순하게 쓰도록 노력해야 합니다.

1. Function Component 바로 안쪽(함수의 최상위)에서만 호출.
2. Function Component 또는 Custom Hook에서만 호출.

### 잘못된 예

처음에는 콜백 함수나 조건문 안에서 Hook을 호출하는 실수를 저지르기 쉽습니다.

```tsx
if (playing) {
  const products = useFetchProducts();
  console.log(products);
}
```

## 참고 자료

[beta 문서의 useRef](https://react.dev/reference/react/useRef)
[공식 문서의 useRef](https://ko.legacy.reactjs.org/docs/hooks-reference.html#useref)
[Reusing Logic with Custom Hooks](https://react.dev/learn/reusing-logic-with-custom-hooks)
[자신만의 Hook 만들기](https://ko.legacy.reactjs.org/docs/hooks-custom.html)
[Hook의 규칙](https://ko.legacy.reactjs.org/docs/hooks-rules.html)
