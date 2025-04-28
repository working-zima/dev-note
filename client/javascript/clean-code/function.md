# 클린코드 for 자바스크립트(함수)

## 1. 함수, 메서드, 생성자

### 함수 (1급 객체)

- 변수나, 데이터에 담길 수 있습니다.
- 매개변수로 전달 가능합니다. (콜백 함수)
- 함수가 함수를 반환할 수 있습니다. (고차 함수)

```javascript
function func() {
  return this;
}

func(); // window or global
```

### 객체의 메서드

- 객체에 의존성이 있는 함수, OOP 행동을 의미합니다.

```javascript
function obj = {
  method: function() {
    return this;
  },
}

obj.method(); // obj
```

#### Concise Methods

concise method를 사용하면 콜론과 함수 키워드를 생략할 수 있습니다.

```javascript
function obj = {
  method() {
    return this;
  },
}

obj.method(); // obj
```

### 생성자 함수 (Class)

- 인스턴스를 생성하는 역할을 합니다.

```javascript
function Func() {
  return this;
}

const instance = new Func(); // Func {}
```

#### class

```javascript
class Func {
  constructor() {
    return this;
  }
}

const instance = new Func(); // Func {}
```

## 2. argument & parameter

parameter(매개변수)는 넘겨받은 argument(인자 or 인수)값으로 초기화됩니다.

```javascript
function example(parameter) {
  console.log(parameter); // Output = foo
}

const argument = "foo";

example(argument);
```

### Parameter (Formal Parameter)

parameter(매개변수)는 함수 정의 부분에 선언된 이름입니다.
매개변수는 타인이 알아볼 수 있도록 형식을 갖추어야 합니다.

```javascript
// url을 받는다는 것을 알 수 있도록 네이밍
function axios(url) {
  // some code
}
```

### Argument (Actual Parameter)

argument(인자 or 인수)는 함수에 전달된 실제 값입니다.

```javascript
function axios(url) {
  // some code
}
axios("https://github.com");
```

## 3. 복잡한 인자 관리

아래의 함수는 4개의 매개변수를 갖지만 맥락을 알기 쉽습니다.

```javascript
function genSquare(top, right, bottom, left) {
  // ...some code
}
```

위의 코드와 동일하게 4개의 매개변수를 갖지만 맥락을 알기 쉽지 않습니다.

```javascript
function createCar(name, brand, color, type) {
  // ...some code
}

createCar("이름", "브랜드", "색깔", "타입");
```

객체 구조 분해 할당을 사용하면 복잡한 인자를 쉽게 관리할 수 있습니다.

```javascript
function createCar({ name, brand, color, type }) {
  // ...some code
}

createCar({ name: "이름", brand: "브랜드", color: "색깔", type: "타입" });
```

`if` 문과 `Error`를 사용하면 인자가 넘어오지 않았을 경우에 방지할 수 있습니다.

```javascript
function createCar({ name, brand, color, type }) {
  if (!color) {
    throw new Error("color is a required");
  }
}

createCar({ name: "이름", brand: "브랜드", type: "타입" });
```

## 4. Default Value

nullish 병합 연산자를 사용해서 인자가 없을 경우 기본값을 설정할 수 있습니다.

```javascript
function createCarousel(options) {
  options = options ?? {};
  let margin = options.margin ?? 0;
  let center = options.center ?? false;
  let navElement = options.navElement ?? "div";
  return {
    margin,
    center,
    navElement,
  };
}

createCarousel(); // { margin: 0, center: false, navElement: 'div'}
```

객체 구조 분해 할당을 사용하면 위의 코드와 동작은 동일하지만 지역변수가 필요 없어집니다.

```javascript
function createCarousel({
  margin = 0,
  center = false,
  navElement = "div",
} = {}) {
  return {
    margin,
    center,
    navElement,
  };
}

createCarousel(); // { margin: 0, center: false, navElement: 'div'}
```

필수로 들어와야하는 값에 대해서는 에러처리도 할 수 있습니다.

```javascript
const required = (argName) => {
  throw new Error(argName + " is required");
};

function createCarousel({
  items = required("items"),
  margin = 0,
  center = false,
  navElement = "div",
} = {}) {
  return {
    margin,
    center,
    navElement,
  };
}

createCarousel(); // items is required
```

## 5. Rest Parameters

화살표 함수가 아닌 경우 arguments를 사용하여 가변 인자를 다룰 수 있습니다.
하지만 추가적인 인자를 받고 싶을 때는 사용할 수 없습니다.

```javascript
function sumTotal() {
  return Array.from(arguments).reduce((acc, curr) => acc + curr);
}

sumTotal(1, 2, 3, 4, 5, 6, 7, 8, 9, 10); // 55
```

Rest Parameters를 사용하면 동일하지만 추가적인 인자도 받을 수 있습니다.
Rest Parameters는 배열이기 때문에 바로 배열 메서드도 사용할 수 있습니다.
단, Rest Parameters는 인자 중 마지막 순서에 위치해야 합니다.

```javascript
function sumTotal(initValue, ...args) {
  return args.reduce((acc, curr) => acc + curr, initValue);
}

sumTotal(100, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10); // 55
```

## 6. void & return

`alert`와 react의 `setState`는 반환이 없습니다.
반환이 없는 메서드를 반환하면 자바스크립트는 `undefined`를 반환됩니다.

```javascript
function handleClick() {
  return setState(false);
}

function handleClick(message) {
  return alert(message);
}
```

반환값이 없는 함수의 타입을 `void`라고 합니다.
`void` 타입인 함수에는 무의미한 `return` 을 사용하지 않는 것이 함수를 명시적으로 사용할 수 있습니다.

```javascript
function handleClick() {
  setState(false);
}

function handleClick(message) {
  alert(message);
}
```

## 7. 화살표 함수

화살표 함수를 사용할 때 주의해야할 상황이 있습니다.

### this

화살표 함수는 Lexical Scope를 사용하기 때문에 화살표 함수의 `this`는 Scope Chain을 통해 상위 스코프의 `this`를 참조합니다.

```javascript
const user = {
  name: "Poco",
  getName: () => {
    return this.name;
  },
};

user.getName(); // undefined
```

### arguments

또한 일반함수와 다르게 `arguments`를 사용할 수 없습니다.
만약 `arguments`를 사용하고 싶다면 매개변수에 spread 연산자를 사용하여 인자에 접근할 수 있습니다.

```javascript
const user = {
  name: "Poco",
  newFriends: () => {
    const newFriendList = Array.from(arguments);
    return this.name + newFriendList;
  },
};

user.newFriends(); // arguments is not defined
```

혹은 앞서 다뤘던 Rest Parameters를 사용하는 것이 좋습니다.

### call, apply, bind

이외에도 `call` `apply`, `bind`를 사용할 수 없기 때문에 주의해야 합니다.

### new

화살표 함수는 생성자 함수로 사용할 수 없습니다.

```javascript
const Person = (name, city) => {
  this.name = name;
  this.city = city;
};

const person = new Person("poco", "korea"); // Person is not a constructor
```

### extends

상속했을 때에도 문제가 있습니다.

```javascript
class Parent {
  parentMethodArrow = () => {
    console.log("parentMethodArrow");
  };
  overrideMethod = () => {
    return "Parent";
  };
}

class Child extends Parent {
  childMethod() {
    super.parentMethodArrow();
  }
  overrideMethod() {
    return "Child";
  }
}

new Child().childMethod(); // parentMethodArrow is not a function
new Child().overrideMethod(); // Parent
```

## 8. callback Function

`addEventListener`에 익명함수를 콜백으로 넘겨주면 대신 실행해줍니다.

```javascript
someElement.addEventListener("click", function (e) {
  console.log(someElement + "이 클릭이 되었습니다.");
});
```

`addEventListener`를 구현해보면 아래와 같습니다.
위 코드의 `e`는 `clickEventObject`에 해당합니다.

```javascript
DOM.prototype.addEventListener = function (eventType, cbFunc) {
  if (eventType === "click") {
    const clickEventObject = {
      target: {},
    };

    // 넘어온 콜백 실행
    cbFunc(clickEventObject);
  }
};
```

### callback을 활용한 리팩터링

`isConfirm`에 따른 함수를 실행하는 공통적인 로직을 가진 함수가 있습니다.
로직은 같지만 실행하는 함수가 다릅니다.

```javascript
function register() {
  const isConfirm = confirm("회원가입에 성공했습니다.");

  if (isConfirm) {
    redirectUserInfoPage();
  }
}

function login() {
  const isConfirm = confirm("로그인에 성공했습니다.");

  if (isConfirm) {
    redirectIndexPage();
  }
}
```

`confirmModal`은 실행할 콜백함수의 제어권을 위임 받아서 대신 실행시켜줄 수 있습니다.
콜백함수는 `confirmModal`가 받아서 실행시켜야 하기 때문에 `()` 없이 넘겨야 합니다.

```javascript
function confirmModal(message, cbFunc) {
  const isConfirm = confirm(message);
  if (isConfirm && cbFunc) {
    cbFunc();
  }
}

function login() {
  confirmModal("회원가입에 성공했습니다.", redirectUserInfoPage);
}
function login() {
  confirmModal("로그인에 성공했습니다.", redirectIndexPage);
}
```

## 9. 순수 함수

순수 함수는 자바스크립트와 같은 프로그래밍 언어에서 중요한 개념 중 하나입니다.
순수 함수는 다음과 같은 특성을 갖습니다.

### 1. side effect가 없음

함수가 외부 상태를 변경하지 않고 오직 주어진 입력 값에만 의존하는 함수를 의미합니다.
side effect는 함수에서 반환 값 이외에 외부에서 볼 수 있는 상태나 동작에 변화를 일으키는 것을 의미합니다.

#### side effect 예시

- 콘솔에 값 출력하기
- 파일 저장하기
- 비동기 타이머 설정하기
- AJAX HTTP 요청 보내기
- 함수 외부에 존재하는 어떤 상태를 수정하거나 함수에 전달된 인자를 변경하기
- 난수 생성 또는 고유한 랜덤 ID 생성하기 (예: Math.random() 또는 Date.now())

### 2. 일관된 반환 값

동일한 입력에 대해 항상 같은 출력을 반환하기 때문에 예측할 수 있습니다.

### 예시

`impureSum1`은 외부에서 내부의 값을 조정할 수 있습니다.
외부에서 내부의 값을 변화시키면 반환 값이 달라지기 때문에 순수 함수가 아닙니다.

```javascript
let num1 = 10;
let num2 = 20;

function impureSum1() {
  return num1 + num2;
}

impureSum1(); // 30
num1 = 30;
impureSum1(); // 50
```

`impureSum1`은 외부에서 내부의 값을 조정할 수 있기 때문에 순수 함수가 아닙니다.

```javascript
let num1 = 10;

function impureSum2(newNum) {
  return num1 + newNum;
}

impureSum1(30); // 40
num1 = 100;
impureSum1(30); // 130
```

`impureSum3`은 내부의 값을 조정할 수 없고 호출할 때마다 값을 예측할 수 있는 순수 함수 입니다.

```javascript
function impureSum3(num1, num2) {
  return num1 + num2;
}

impureSum3(10, 20); // 30
impureSum3(30, 100); // 130
```

순수 함수입니다.

```javascript
function changeValue(num) {
  num++;
  return num;
}

changeValue(1); // 2
changeValue(3); // 4
```

원본 값을 바꾸기 때문에 순수 함수가 아닙니다.
참조값을 사용할 때는 항상 새로운 객체를 만들어서 반환해야합니다.

```javascript
const obj = { one: 1 };

function changeObj(targetObj) {
  targetObj.one = 100;
  return targetObj;
}

changeObj(obj); // { one: 100 }
obj; // { one: 100 }
```

위의 코드를 순수함수로 바꿔보겠습니다.
spread operator를 사용하여 새로운 객체를 만들어서 반환했습니다.

```javascript
const obj = { one: 1 };

function changeObj(targetObj) {
  targetObj.one = 100;
  return { ...targetObj, one: 100 };
}

changeObj(obj); // { one: 100 }
obj; // { one: 1 }
```

## 10. Closure

Closure는 함수가 생성될때 같이 발생하는 특별한 현상이며,
어떤 컨텍스트에서 선언한 변수를 참조하는 내부함수를 컨텍스트의 외부로 전달할 경우에 발생합니다.

이러한 특별한 현상은 함수(컨텍스트) 종료 후에도 사라지지 않는 지역변수를 만들 수 있습니다.

### 예시1

`addOne`은 `add` 함수를 실행하고 반환 받은 값을 가지고 있습니다.
`add` 함수는 이미 종료되었지만 내부의 `sum` 함수는 `addOne` 안에서 살아있습니다.

```javascript
function add(num1) {
  return function sum(num2) {
    return num1 + num2;
  };
}

const addOne = add(1);
const addTwo = add(1)(3); // 4

addOne; //  sum(num2) {return num1 + num2;}
addOne(3); // 4
```

### 예시2

응용버전입니다.
`add`라는 공통된 함수를 사용하지만 `addOne` 에서 5와 2를 가진 컨텍스트를 캡쳐하고 있다가 `sum`과 `multiple`이 들어왔을 때 각자의 컨텍스트를 갖게 됩니다.

```javascript
function add(num1) {
  return function (num2) {
    return function (calculateFn) {
      return calculateFn(num1, num2);
    };
  };
}

function sum(num1, num2) {
  return num1 + num2;
}

function multiple(num1, num2) {
  return num1 * num2;
}

const addOne = add(5)(2);
const sumTwo = addOne(sum); // 7
const sumMultiple = addOne(multiple); // 10
```

### 예시3

closure를 활용해서 `if` 문 없이 `console`의 종류를 바꿔서 사용할 수 있습니다.

```javascript
function log(value) {
  return function (fn) {
    fn(value);
  };
}

const logFoo = log("foo");

logFoo((v) => {
  console.log(v);
});
logFoo((v) => {
  console.info(v);
});
logFoo((v) => {
  console.error(v);
});
logFoo((v) => {
  console.warn(v);
});
```

### 예시4

매번 `isTypeOf` 함수를 호출할 때마다 `typeof value === type`를 검사합니다.

```javascript
const arr = [1, 2, 3, "A", "B", "C"];

function isTypeOf(type, value) {
  return typeof value === type;
}

const isNumber = (value) => isTypeOf("number", value);
const isString = (value) => isTypeOf("string", value);

arr.filter(isNumber); // [1, 2, 3]
arr.filter(isString); // ['A', 'B', 'C']
```

아래의 리팩토링한 함수는 closure를 사용하여 type 정보를 유지합니다.
`isTypeOf('number')` 또는 `isTypeOf('string')`를 호출하여 반환된 함수가 이미 `type`을 기억하고 있으므로 그때마다 다시 타입을 지정할 필요가 없습니다.

```javascript
const arr = [1, 2, 3, 'A', 'B', 'C'];

function isTypeOf(type) {
  reutn function (value) {
    return typeof value === type;
  }
}

const isNumber = isTypeOf('number')
const isString = isTypeOf('string')

arr.filter(isNumber); // [1, 2, 3]
arr.filter(isString); // ['A', 'B', 'C']
```

### 예시5

`naverApi`와 `daumApi`는 baseUrl을 기억하고 있기 때문에 사용할 때 baseUrl을 다시 입력할 필요가 없습니다.

```javascript
function fetcher(endpoint) {
  return function (url, options) {
    return fetch(endpoint + url, options)
      .then((res) => {
        if (res.ok) {
          return res.json();
        } else {
          throw new Error(res.error);
        }
      })
      .catch((err) => console.error(err));
  };
}

const naverApi = fetcher("http://naver.com/");
const daumApi = fetcher("http://www.daum.net/");

naverApi("/webtoon").then((res) => res);
daumApi("/webtoon").then((res) => res);
```

### 예시6

```javascript
someElement.addEventListener("click", debounce(handleClick, 500));

someElement.addEventListener("click", throttle(handleClick, 500));
```

## 참고

[클린코드 자바스크립트(JavaScript)](https://www.udemy.com/course/clean-code-js/)
