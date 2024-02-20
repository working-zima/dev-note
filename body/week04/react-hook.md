# 3. React의 Hook

## 학습 키워드

- React Hook 이란
- Hooks
  - useState
  - useEffect
  - useContext
  - useRef
  - useLayoutEffect
- React StrictMode 란

## React의 Hook 이란

React 16.8에서 Hooks가 도입됐습니다.\
기존 방식에 있던 몇 가지 문제를 해결했습니다.

### 기존 방식의 문제점

- Wrapper Hell (Higher Order Components)\
  : 고차 컴포넌트(HoC)는 컴포넌트 로직을 재사용하기 위해 사용되었습니다.\
  그러나 각 고차 컴포넌트(컴포넌트를 취하여 새로운 컴포넌트를 반환하는 함수)가 컴포넌트에 추가되면서 컴포넌트의 역할을 파악하기 어려워지고, 코드를 이해하기 어려운 상황이 발생합니다.

- Huge Components\
  : 클래스 컴포넌트 안에서 상태(state), 생명주기 메서드, 그리고 UI 렌더링과 관련된 로직이 모두 함께 정의되었습니다.\
  이러한 구조로 컴포넌트의 크기가 커지면서 유지보수가 어려우며, 특정 기능을 이해하고 수정하기 어려웠습니다.

- Confusing Classes\
  : 클래스 기반의 컴포넌트는 상태 변화 및 생명주기 관리를 위한 복잡한 코드를 유발하고, 이해하기 어려웠습니다.

### React 16.8 이후

React를 쓰는 방식을 완전히 바꾼 커다란 변화.

→ 이제는 예전으로 돌아가는 게 불가능하다!

#### 기존

- 상태를 가진 컴포넌트는 Class Component로 만들고, props만 사용하는 재사용이 용이한 작은 컴포넌트는 Function Component로 작성.
- Redux에서도 비슷한 구분이 존재했다.
  - [Presentational and Container Components - Dan Abramov](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)

#### 현재

- 그냥 Function Component만 사용.
- 상태 관리 유무를 바로 알기 어려움 = 신경쓰지 않아도 됨.
- 복잡한 요소는 전부 Hook으로 격리 및 재사용 가능.

#### 대표적인 Hooks

- useState → State Hook ⇒ React의 State
- useEffect ⇒ Side-effect
- useContext
- useRef
- useLayoutEffect → useEffect와 조금 다름.

## useState

3주차 [React State](../week03/react-state.md)에서 다뤘던 내용입니다.

## useEffect

어떤 컴포넌트는 렌더링 이후 해야 할 일, 즉 React의 외부와 관련된 일을 정해줄 수 있습니다.\
기본적으로 렌더링 때마다 실행되므로, 의존성 배열을 통해 언제 이펙트를 실행할지 지정할 수 있습니다.\
(= 불필요한 경우에 건너뛸 수 있습니다)\
함수를 리턴함으로써 종료 처리를 할 수 있습니다.

### 타이머 예제

React의 외부에 우아하게 접근.\
이 정도는 `useEffect`를 안 쓴다고 크게 문제가 되지 않지만, 이렇게 쓰는 습관을 들이자.

```jsx
useEffect(() => {
  document.title = `Now: ${new Date().getTime()}`;
});
```

타이머를 on/off하는 기능을 그냥 만들면 문제가 발생한다.

```jsx
function Timer() {
  useEffect(() => {
    setInterval(() => {
      document.title = `Now: ${new Date().getTime()}`;
    }, 100);
  });

  return <p>Playing</p>;
}

export default function TimerControl() {
  const [playing, setPlaying] = useState(false);

  const handleClick = () => {
    setPlaying(!playing);
  };

  return (
    <div>
      {playing ? <Timer /> : <p>Stop</p>}
      <button type="button" onClick={handleClick}>
        Toggle
      </button>
    </div>
  );
}
```

종료 처리

```jsx
useEffect(() => {
  const savedTitle = document.title;

  const id = setInterval(() => {
    document.title = `Now: ${new Date().getTime()}`;
  }, 100);

  return () => {
    document.title = savedTitle;
    clearInterval(id);
  };
});
```

### 처음에 한번만 실행하기

의존성 배열에서 아무 것도 지정하지 않으면 맨 처음에 딱 한번만 실행하게 할 수 있습니다.\
**주로 API를 호출해서 데이터를 얻을 때 사용한다.**

```tsx
const [products, setProducts] = useState<Product[]>([]);

useEffect(() => {
  const fetchProducts = async () => {
    const url = "http://localhost:3000/products";
    const response = await fetch(url);
    const data = await response.json();
    setProducts(data.products);
  };

  fetchProducts();
  // 빈 배열
}, []);
```

Fetch 함수의 위치가 고민된다면, [Dan Abramov의 글](https://rinae.dev/posts/a-complete-guide-to-useeffect-ko/#%ed%95%a8%ec%88%98%eb%a5%bc-%ec%9d%b4%ed%8e%99%ed%8a%b8-%ec%95%88%ec%9c%bc%eb%a1%9c-%ec%98%ae%ea%b8%b0%ea%b8%b0)을 다시 보자.

아래의 코드와 위의 코드는 별반 다르지 않습니다.

```tsx
const fetchProducts = async () => {
  const url = "http://localhost:3000/products";
  const response = await fetch(url);
  const data = await response.json();
  setProducts(data.products);
};

useEffect(() => {
  fetchProducts();
}, []);
```

하지만 혹시라도 `useEffect` 밖에 있던 함수들 중에 하나가 `state`나 `prop`을 사용하게 되면 `prop`과 `state`의 변화에 동기화하는데 실패하게 됩니다.

### Effect가 두 번 실행되는 문제

`<React.StrictMode>`로 컴포넌트 전체를 감쌀 경우, 예상치 못한 Side Effect를 찾으려고 Effect 등을 두 번씩 실행합니다. 평소에는 큰 문제가 없지만, API 등을 사용하면 이상하다고 느낄 수 있으니 참고할 것.
`<React.StrictMode>`는 개발할 때만 작동합니다.

### 의존성 배열을 이용해 Fetch할 때 주의사항

`useEffect` 안에서 비동기 작업을 수행할 때, 해당 작업이 완료되기 전에 또 중복된 작업이 실행된다면 "메모리 누수(memory leak)"나 "의도치 않은 동작"이 발생할 수 있습니다.\
따라서 `useEffect` 내에서 비동기 작업을 수행할 때는 컴포넌트가 언마운트되었을 때에 대비하여 cleanup 함수를 구현해야 합니다.\
일반적으로 useEffect 내에서 사용되는 변수나 상태를 이용하여 언마운트 여부를 확인하고, cleanup 함수에서 해당 변수나 상태를 업데이트하여 비동기 작업이 완료된 후에는 해당 작업을 무시하도록 합니다.

```tsx
useEffect(() => {
  let ignore = false;

  async function startFetching() {
    const json = await fetchTodos(userId);
    if (!ignore) {
      setTodos(json);
    }
  }

  startFetching();

  return () => {
    ignore = true;
  };
}, [userId]);
```

Cleanup 함수 내에서 언마운트 여부를 확인하는 변수(ignore 등)를 활용하여 작업의 완료 여부를 체크하고, 언마운트된 경우에는 해당 작업을 무시하도록 구현합니다.

## 참고 자료

[Hook의 개요](https://ko.reactjs.org/docs/hooks-intro.html)\
[Hook 개요](https://ko.reactjs.org/docs/hooks-overview.html)\
[Hooks API Reference](https://ko.reactjs.org/docs/hooks-reference.html)\
[React Conf 2018 Hooks 소개 영상](https://youtu.be/dpw9EHDh2bM)\
[HoC (Higher-Order Components)](https://ko.reactjs.org/docs/higher-order-components.html)\
[Synchronizing with Effects](https://beta.reactjs.org/learn/synchronizing-with-effects)\
[You Might Not Need an Effect](https://beta.reactjs.org/learn/you-might-not-need-an-effect)\
[Using the Effect Hook](https://ko.reactjs.org/docs/hooks-effect.html)\
[useEffect](https://beta.reactjs.org/reference/react/useEffect)\
[useEffect 완벽 가이드](https://overreacted.io/a-complete-guide-to-useeffect/)\
[[번역] useEffect 완벽 가이드](https://rinae.dev/posts/a-complete-guide-to-useeffect-ko/)\
[예상치 못한 부작용 검사](https://ko.reactjs.org/docs/strict-mode.html#detecting-unexpected-side-effects)
[Fetching data](https://beta.reactjs.org/learn/synchronizing-with-effects#fetching-data)
