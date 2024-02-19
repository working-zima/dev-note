# 2. React State

## 학습 키워드

- React state란?
- DRY 원칙
- SSOT(Single Source of Truth)
- useState
- 1급 객체(first-class object)란?
- Lifting State Up

## 또 Thinking in React

- “Step 3: 단순하지만 완전한 UI 상태 표현 찾기”
- “Step 4: 상태가 어디에 위치해야 하는지 확인하기”
- “Step 5: 역방향 데이터 흐름 추가하기”

## React의 State란?

React의 state는 “변경”을 다루기 위한 요소입니다.\
“Re-rendering”이란 주제에서 다뤄집니다.\
어떤 컴포넌트의 state가 바뀌면 해당 컴포넌트와 하위 컴포넌트를 다시 렌더링하게 됩니다.

아무렇게나 만들어도 되지만, 일관성과 효율을 위해 DRY 원칙을 따르는 SSOT를 만듭니다.

### DRY 원칙

중복을 최소화하고 코드의 재사용성을 높이는 것을 목표로 합니다.\

1. 컴포넌트 재사용\
: 비슷한 UI 요소가 여러 곳에서 사용되는 경우 해당 요소를 컴포넌트로 추상화하고 여러 곳에서 재사용할 수 있습니다.
2. 컴포넌트 상속 및 확장\
: React 클래스 컴포넌트를 사용하는 경우, 컴포넌트 상속을 통해 공통 기능을 가진 부모 컴포넌트를 만들고, 이를 상속하여 여러 자식 컴포넌트에서 재사용할 수 있습니다.
3. 코드 분리와 모듈화\
: 코드를 여러 파일이나 모듈로 나누어 관리함으로써 중복을 방지할 수 있습니다.
4. 고차 컴포넌트 (Higher Order Components, HOC)\
: 함수를 인자로 받아 컴포넌트를 반환하는 함수입니다.\
이를 사용하여 여러 컴포넌트에서 공통 로직을 추상화할 수 있습니다.

### SSOT(Single Source of Truth)

상태의 중앙 집중화된 저장소를 가리키며, 애플리케이션의 상태 정보가 단일한 출처에서 관리된다는 개념입니다.\
모든 데이터를 상태로 두면 편하지만 여러 컴포넌트에서 중복된 상태를 관리하는 경우 여러 문제들이 발생할 수 있습니다.\
불필요한 리렌더링은 성능에 부정적인 영향을 미치며 데이터의 일관성을 유지하기 어려워지면서 어플리케이션의 전체적인 상태를 이해하고 관리하기 어려워집니다.\
또한 코드가 복잡해지고 유지보수가 어려워질 수 있습니다.\
따라서 필요한 상태만 관리함으로써 코드를 간결하게 유지하는 것이 좋습니다.

### useState

useState는 컴포넌트에 state 변수를 추가할 수 있게 해주는 React 훅입니다.\
클래스 컴포넌트에서 사용되던 this.state와 this.setState에 대응됩니다.\
배열 구조 분해를 사용하여 [something, setSomething]과 같은 state 변수의 이름을 지정하는 것이 관례이며 컴포넌트의 최상위 레벨에서 useState를 호출하여 state 변수를 선언하여 사용합니다.

```jsx
import { useState } from 'react';

function MyComponent() {
  const [age, setAge] = useState(28);
  const [name, setName] = useState('Taylor');
  const [todos, setTodos] = useState(() => createTodos());
  // ...
}
```

### React State의 조건

1. 변경되는 데이터여야 합니다.\
변경되지 않는 건 state로 다룰 가치가 없습니다.

2. 부모 컴포넌트가 props를 통해 전달할 수 있다면 state가 아닙니다.

3. 다른 state나 props를 이용해 계산 가능하다면 state가 아닙니다.

```tsx
import ProductsInCategory from './ProductsInCategory';
import Product from '../types/Product';
import selectCategories from '../utils/selectCategories';

// 안좋은 예
type ProductTableProps = {
  products: Product[];
}

function ProductTable({ products }: ProductTableProps) {
  // props를 state로 사용하게 되면서 업데이트를 위해 useEffect까지 사용하게 됩니다.
  const [categories, setCategories] = useState<String[]>([]);

  useEffect(() => {
    setCategories(selectCategories(products))
  }, [products])

  return (
    <table className="products-table">
      <thead>
        <tr>
          <th>Name</th>
          <th>Price</th>
        </tr>
      </thead>
      <tbody>
        {categories.map((category) => (
          <ProductsInCategory
            key={category}
            category={category}
            products={products}
          />
        ))}
      </tbody>
    </table>
  );
}


// 좋은 예
type ProductTableProps = {
  products: Product[];
}

function ProductTable({ products }: ProductTableProps) {
  // props를 이용해 계산(selectCategories) 가능하기 때문에
  // 상위 state가 업데이트 되면 props가 다시 계산 되도록 합니다.
  const categories = selectCategories(products);

  return (
    <table className="products-table">
      <thead>
        <tr>
          <th>Name</th>
          <th>Price</th>
        </tr>
      </thead>
      <tbody>
        {categories.map((category) => (
          <ProductsInCategory
            key={category}
            category={category}
            products={products}
          />
        ))}
      </tbody>
    </table>
  );
}
```

다루는 상태가 너무 많으면 복잡합니다.\
TypeScript를 잘 쓰면 직접 관리하는 상태의 수를 줄여줄 수 있습니다.\
그렇다면 그 상태를 누가 관리해야 할까? 더 정확히는, 상태를 누가 소유해야 할까?\
(React만 쓴다면) 해당 상태에 의존적인 컴포넌트를 모두 포함하는 최상위 컴포넌트가 상태를 소유해야 합니다.(Lifting State Up)

### Lifting State Up

두 개 이상의 컴포넌트 간에 상태를 공유하기 위해 주로 lifting state up 이라는 개념을 사용합니다.

1. 두 컴포넌트를 조정하려면 상태를 공통 부모로 이동시킵니다.\
두 컴포넌트 간의 상태를 동시에 변경하려면 해당 상태를 두 컴포넌트의 공통 부모로 이동시킵니다.\

2. 그 후 공통 부모로부터 정보를 props로 전달합니다.\
공통 부모 컴포넌트로 상태를 끌어올렸다면, 해당 상태 정보를 자식 컴포넌트로 props를 통해 전달합니다.

3. 마지막으로 이벤트 핸들러를 전달하여 자식이 부모의 상태를 변경할 수 있도록 하세요.\
자식 컴포넌트에서 부모의 상태를 변경해야 할 경우, 부모가 정의한 이벤트 핸들러를 자식 컴포넌트로 전달하여 상태를 변경하게 합니다.

### Inverse Data Flow

하위 컴포넌트의 props로 함수를 전달. 흔히 콜백 함수라고 부름.

TypeScript(정확히는 JavaScript)는 함수가 일급(first-class) 객체다. 즉, 어떤 함수를 다른 함수에 인자로 넘겨주거나, 어떤 함수를 리턴값으로 사용할 수 있다. 익명 함수, 클로저 등과 함께 사용하면 시너지가 큼.

### 1급 객체(first-class object)란?

일급 시민(first-class citizen 혹은 type,object,entity, value 라고도 합니다.)이란 무슨 혜택을 받는다는 게 아니라, 사용할 때 다른 요소들과 아무런 차별이 없다는 것을 뜻합니다.

#### Robin Popplestone의 1급 객체 기준

- 모든 1급 객체는 함수의 실질적인 매개변수가 될 수 있다.
- 모든 1급 객체는 함수의 반환값이 될 수 있다.
- 모든 1급 객체는 할당의 대상이 될 수 있다.
- 모든 1급 객체는 비교 연산(==, equal)을 적용할 수 있다.

### 1급 함수(first-class function)란?

프로그래밍 언어는 해당 언어의 함수들이 다른 변수처럼 다루어질 때 일급 함수를 가진다고 합니다.\
일급 함수를 가진 언어에서 함수는 변수처럼 다른 함수들에 전달인자로 제공되고, 다른 함수에 의해 반환될 수 있으며, 변수에 값으로서 할당될 수 있습니다.

- 변수에 함수 할당

```jsx
const foo = () => {
  console.log("foobar");
};
foo(); // 변수를 사용해 호출
// foobar
```

- 함수에 인자로 전달

```jsx
function sayHello() {
  return "Hello, ";
}
function greeting(helloMessage, name) {
  console.log(helloMessage() + name);
}
// `sayHello`를 전달인자로 `greeting` 함수에 전달
greeting(sayHello, "JavaScript!");
// Hello, JavaScript!

```

- 함수 반환

```jsx
function sayHello() {
  return () => {
    console.log("Hello!");
  };
}
```

### 참고

아이템 목록에서 key가 value인 것만 선택하기.

```jsx
function select<ItemType, ValueType>(
  items: ItemType[],
  key: keyof ItemType,
  value: ValueType,
) {
  return items.filter((item) => item[key] === value);
}
```

## 참고 자료

- [Thinking in React](https://beta.reactjs.org/learn/thinking-in-react)
- [DRY (Don’t Repeat Yourself)](https://ko.wikipedia.org/wiki/중복배제)
- [SSOT (Single Source of Truth)](https://ko.wikipedia.org/wiki/단일_진실_공급원)
- [“Lifting State Up”](https://ko.reactjs.org/docs/lifting-state-up.html)
- [Sharing State Between Components](https://beta.reactjs.org/learn/sharing-state-between-components)
- [일급 함수](https://developer.mozilla.org/ko/docs/Glossary/First-class_Function)
- [1급 객체(first-class object)란?](https://jcsoohwancho.github.io/2019-10-18-1%EA%B8%89-%EA%B0%9D%EC%B2%B4(first-class-object)%EC%9D%B4%EB%9E%80/)