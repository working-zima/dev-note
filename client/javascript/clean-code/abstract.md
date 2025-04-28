# 클린코드 for 자바스크립트(추상화)

## Magic Number

snake case를 사용한 상수로 특정 숫자를 초기화하여 사용하면 숫자의 의미를 알 수 있고 주의 해야하는 값이라는 것을 알릴 수 있습니다.

```javascript
const COMMIN_DELAY_MS = 3 * 60 * 1000;

setTimeout(() => {
  scrollToTop();
}, COMMIN_DELAY_MS);
```

### Numeric Operator

숫자에 Underscore를 사용해서 파악하기 쉽게 표시할 수 있습니다.

```javascript
const PRICE = {
  MIN: 1_000_000,
  MAX: 100_000_000,
};

console.log(PRICE); // { MIN:1000000, MAX: 100000000 }
```

## 네이밍 컨벤션

저장소, 폴더, 함수, 변수, 상수, 깃 브랜치, 커밋 등 프로그래밍 전반적으로 이름을 네이밍을 위한 규칙이나 관습을 만드는 것입니다.

### 대표적인 케이스

- #### camelCase

  자바스크립트

- #### PascalCase

  함수 생성자, 클래스, 컴포넌트명

- #### kebab-case

  npm 패키지명, 웹url

- #### SNAKE_CASE

  상수명, enum

### 접두사, 접미사

#### prefix-\*

- dataset 프로퍼티
  data-id, data-name, data-value

- 타입 네이밍
  ICAR, TCAR

#### \*-suffix

- Container
  AppContainer, BoxContainer

- Component
  ListComponent, ItemComponent

#### 동사-\*

함수명은 동사로 시작합니다.
함수를 인자로 넘길 수 있기 때문에 변수와 구분할 수 있습니다.

### 연속적인 규칙

`for` 루프에 사용되는 변수는 알파벳 순으로 `i`부터 시작합니다.

```javascript
for (let i = 0; i < 10; i++) {
  for (let j = 0; j < 10; j++) {
    for (let k = 0; k < 10; k++) {}
  }
}
```

타입스크립트의 Generic은 알파벳 순으로 `T`부터 시작합니다.
(경우에 따라 `T1`, `T2`, `T3`로 사용하는 경우도 있습니다.)

```javascript
function func<T, U>(name: T, value: U)
```

### 자료형 표현

자료형의 타입을 유추할 수 있도록 네이밍을 합니다.

```javascript
const inputNumber = 10;
const someArr = [];
const strToNum = "some code";
```

### 이벤트 표현

```javascript
function on-*
function handle-*
function *-Action
function *-Event
function take-*
function *-Query
function *-All
```

### CRUD

```javascript
function generator-*
function gen-*
function make-*
function get
function set
function remove
function create
function delete
```

### Flag

특정 시점, if문의 구분자 등으로 사용됩니다.

```javascript
const isSubmit
const isDisabled
const isString
const isNumber
```

가장 중요한 것은 자바스크립트의 예약어, 키워드와 겹치지 않는지 확인해야합니다.

## DOM API 접근 추상화

### HTML에 접근하는 JavaScript 코드 추상화

코드를 작성하다 보면 Javascript만으로 HTML과 CSS를 다루게 됩니다.
문제는 HTML과 CSS 코드가 난발되고 흩어지게 되는 경우가 많습니다.
아래의 경우, HTML을 조작하는 코드와 CSS 코드를 잘 감추었습니다.

```javascript
const createLoader = () => {
  const el1 = document.createElement("div");
  const el2 = document.createElement("div");
  const el3 = document.createElement("div");
  return { el1, el2, el3 };
};

const createLoaderStyle = ({ elements }) => {
  el1.setAttribute("class", "loading d-flex justify-center mt-3");
  el2.setAttribute("class", "relative spinner-container");
  el3.setAttribute("class", "material spinner");
  return { newEl1: el1, newEl2: el2, newEl3: el3 };
};

const loader = () => {
  const { el1, el2, el3 } = createLoader();
  const { newEl1, newEl2, newEl3 } = createLoaderStyle({ el1, el2, el3 });
  newEl.append(newEl2);
  newEl2.append(newEl3);

  return newEl;
};
```

## 참고

[클린코드 자바스크립트(JavaScript)](https://www.udemy.com/course/clean-code-js/)
