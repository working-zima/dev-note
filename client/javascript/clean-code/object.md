# 클린코드 for 자바스크립트(객체)

## 1. Shorthand Properties

css에서 `background-color`, `background-image`, `background-repeat`, `background-position`은 `background` 로,
`margin-top`, `margin-right`, `margin-bottom`, `margin-left`는 `margin` 으로 suffix를 생략하며 축약할 수 있습니다.

css의 사례와 같이 축약하면 값을 간결하게 표현할 수 있습니다.

아래는 축약되지 않은 프로퍼티와 메서드를 가진 객체입니다.

```javascript
const firstName = "poco";
const lastName = "jang";

const person = {
  firstName: "poco",
  lastName: "jang",
  getFullName: function () {
    return this.firstName + " " + this.lastName;
  },
};
```

Shorthand Properties를 사용해 프로퍼티를 축약할 수 있습니다.

```javascript
const firstName = "poco";
const lastName = "jang";

const person = {
  // Shorthand Properties
  firstName,
  lastName,
  getFullName: function () {
    return this.firstName + " " + this.lastName;
  },
};
```

### Concise Method

자바스크립트의 메서드를 축약하여 명시적으로 만들어 줍니다.

```javascript
const firstName = 'poco';
const lastName = 'jang';

const person = {
  firstName,
  lastName,
  // Concise Method
  getFullName: () {
    return this.firstName + ' ' + this.lastName;
  },
};
```

리덕스의 Reducer에서도 축약을 사용합니다.

```javascript
const counterApp = combineReducers({
  counter,
  extra,
});
```

원래 모습은 아래와 같습니다.

```javascript
const counterApp = combineReducers({
  counter: counter,
  extra: extra,
});
```

## 2. Computed Property Name

Computed Property Name은 JavaScript에서 객체의 속성 이름을 동적으로 계산할 수 있게 해주는 기능입니다.

### 예시1

React입니다.

```jsx
const [state, setState] = useState({
  id: "",
  password: "",
});

const handle = (e) => {
  setState({
    [e.target.name]: e.target.value,
    // [e.target.name]은 name과 password
    // e.target.value은 state.id와 state.password
  });
};

return (
  <React.Fragment>
    <input value={state.id} onChange={handleChange} name="name" />
    <input value={state.password} onChange={handleChange} name="password" />
  </React.Fragment>
);
```

### 예시2

redux의 handleActions입니다.

```jsx
const noop = createAction("INCREMENT");

const reducer = handleActions(
  {
    [noop]: (state, action) => ({
      counter: state.counter + action.payload,
    }),
  },
  { counter: 0 }
);
```

### 예시3

Vue에서 함수명을 Computed Property Name으로 하였습니다.

```jsx
import Vuex from "vuex";
import { SOME_MUTATION } from "./mutation-types";

export const SOME_MUTATION = "SOME_MUTATION";

const store = new Vuex.Store({
  state: {
    // some code...
  },
  mutations: {
    [SOME_MUTATION](state) {},
  },
});
```

### 예시4

```javascript
const funcName0 = "func0";
const funcName1 = "func1";
const funcName2 = "func2";

const obj = {
  [funcName0]() {
    return "func0";
  },
  [funcName1]() {
    return "func1";
  },
  [funcName2]() {
    return "func2";
  },
};

for (let i = 0; i < 3; i++) {
  console.log(obj[`func${i}`]()); // func0, func1, func2
}
```

## 3. Lookup Table

기능과 스펙이 늘어날 때마다 `if`문이 늘어나게 됩니다.

```javascript
function getUserType(type) {
  if (type === "ADMIN") {
    return "관리자";
  }
  if (type === "INSTRUCTOR") {
    return "강사";
  }
  if (type === "STUDENT") {
    return "수강생";
  }
  return "해당 없음";
}
```

`switch`문도 별반 다르지 않습니다.

```javascript
function getUserType(type) {
  switch (key) {
    case "ADMIN":
      return "관리자";
    case "INSTRUCTOR":
      return "강사";
    case "STUDENT":
      return "수강생";
    default:
      return "해당 없음";
  }
}
```

Computed Property Name을 활용한 Lookup Table을 사용하면 이점이 많습니다.

```javascript
function getUserType(type) {
  const USER_TYPE = {
    ADMIN: "관리자",
    INSTRUCTOR: "강사",
    STUDENT: "수강생",
    UNDEFINED: "해당 없음",
  };
  return USER_TYPE[type] ?? USER_TYPE[UNDEFINED];
}
```

Lookup Table에서 상수 `USER_TYPE`을 다른 파일에서 관리하면 기능과 스펙이 늘어날 때에도 불필요한 분기문을 늘리지 않을 수 있습니다.

```javascript
import USER_TYPE from './constants/some.js

function getUserType(type) {
  return USER_TYPE[type] ?? USER_TYPE[UNDEFINED];
}
```

팩토리 함수로 사용하면 코드를 축소할 수 있습니다.

```javascript
function getUserType(type) {
  return (
    {
      ADMIN: "관리자",
      INSTRUCTOR: "강사",
      STUDENT: "수강생",
    }[type] ?? "해당 없음"
  );
}
```

## 4. Object Destructuring

아래의 함수의 인자는 위치가 강제됩니다.

```javascript
function Person(name, age, location) {
  this.name = name;
  this.age = age;
  this.location = location;
}

const poco = new Person("poco", 30, "korea");
```

만약 객체로 인자를 넘겨주게 된다면 순서를 지키지 않아도 됩니다.

```javascript
function Person({ name, age, location }) {
  this.name = name;
  this.age = age;
  this.location = location;
}

const poco = new Person({
  name: "poco",
  age: 30,
  location: "korea",
});
```

만약 name을 필수적으로 받고 나머지 인자를 옵션이라면 아래와 같이 인자를 받을 수 있습니다.

```javascript
function Person(name, options) {
  this.name = name;
  this.age = options.age;
  this.location = options.location;
}

const pocoOptions = {
  age: 30,
  location: "korea",
};

const poco = new Person("poco", pocoOptions);
```

객체 분해할당을 사용하면 더욱 명시적으로 코드를 사용할 수 있습니다.

```javascript
function Person(name, { age, location }) {
  this.name = name;
  this.age = age;
  this.location = location;
}

const pocoOptions = {
  age: 30,
  location: "korea",
};

const poco = new Person("poco", pocoOptions);
```

배열 분해할당을 사용할 때 필요에 따라 순서를 건너 뛰고 싶을 때가 있습니다.

```javascript
const orders = ["First", "Second", "Third"];

const st = orders[0];
const rd = orders[2];

// Second는 필요없음
const [first, , third] = orders;

console.log(first); // First
console.log(third); // Third
```

객체 분해할당을 사용하면 불필요한 쉼표를 사용하지 않을 수 있습니다.

```javascript
const orders = ["First", "Second", "Third"];

const { 0: first, 2: third } = orders;

console.log(first); // First
console.log(third); // Third
```

## 5. Object.freeze()

객체를 동결하여 동결된 객체를 더 이상 변경될 수 없게 합니다.

```javascript
const STATUS = Object.freeze({
  PENDING: "PENDING",
  SUCCESS: "SUCCESS",
  FAIL: "FAIL",
  OPTIONS: {
    GREEN: "GREEN",
    RED: "RED",
  },
});
```

Object.isFrozen()으로 객체가 동결이 되어 있는지 확인할 수 있습니다.
중첩 객체는 동결되지 않습니다.(깊은 복사, 얕은 복사)

```javascript
Object.isFrozen(STATUS.SUCCESS); // true

Object.isFrozen(STATUS.OPTIONS); // false
```

### 중첩 freeze 사용 방법

1. 라이브러리 사용(lodash)
2. 직접 유틸 함수를 생성하여 사용
3. TypeScript => readonly 사용

```javascript
// 유틸 함수 예시

const STATUS = deepFreeze({
  PENDING: 'PENDING',
    SUCCESS: 'SUCCESS',
    FAIL: 'FAIL',
    OPTIONS: {
      GREEN: 'GREEN',
        RED: 'RED',
    },
});


const deepFreeze(targetObj){
  // 1. 객체를 순회
  // 2. 값이 객체인지 확인
  // 3. 객체이면 재귀
  // 4. 그렇지 않으면 Object.freeze
  Object.keys(targetObj).forEach(key => {
    if(typeof key === 'object' && !Array.isArray(key) && key !== null) {
      deepFreeze(targetObj[key])
    }
    return
  })
  return Object.freeze(targetObj)
}
```

## 6. Prototype 조작 지양

예전의 자바스크립트는 ProtoType을 사용해서 클래스를 흉내냈습니다.

```javascript
function Car(name, brand) {
  this.name = name;
  this.brand = brand;
}

Car.prototype.sayName = function () {
  return this.brand + "-" + this.name;
};

const casper = new Car("캐스퍼", "현대");
```

지금의 자바스크립트에서는 ProtoType을 사용할 필요가 없습니다.

```javascript
class Car {
  constructor(name, brand) {
    this.name = name;
    this.brand = brand;
  }

  sayName() {
    return this.brand + "-" + this.name;
  }
}

const casper = new Car("캐스퍼", "현대");
```

또한 직접 prototype을 조작하면서 변수명이나 메서드명이 충돌할 수 있습니다.
따라서 JS 빌트인 객체를 건들지 않는 것이 좋습니다.
만약 필요한 기능이 있다면 prototype을 직접 조작하는 것보다 모듈화를 통한 방법으로 사용하는 것이 안전합니다.

## 7. hasOwnProperty

hasOwnProperty는 객체가 key에 대응하는 프로퍼티를 직접 구현되어 있는 속성으로 갖고 있는지(상속 프로퍼티가 아닌) 여부를 나타내는 boolean 값을 반환합니다.

```javascript
const object1 = {};
object1.property1 = 42;

console.log(object1.hasOwnProperty("property1"));
// Expected output: true

console.log(object1.hasOwnProperty("toString"));
// Expected output: false

console.log(object1.hasOwnProperty("hasOwnProperty"));
// Expected output: false
```

### 문제점

JavaScript에서 `hasOwnProperty`라는 속성 이름은 보호되지 않습니다.
따라서 `hasOwnProperty`라는 이름을 가진 속성을 가진 객체는 원하지 않는 결과를 반환할 수 있습니다.

```javascript
const foo = {
  hasOwnProperty() {
    return false;
  },
  bar: "Here be dragons",
};

foo.hasOwnProperty("bar"); // 다시 구현된 hasOwnProperty는 항상 false를 반환
```

### 해결법

1. `Object.hasOwn()`을 사용하는 것

2. `Object.prototype`에서 직접 `hasOwnProperty`를 사용

```javascript
const foo = { bar: "Here be dragons" };

// Object.hasOwn() 메서드 사용 - 권장됨
Object.hasOwn(foo, "bar"); // true

// foo객체의 hasOwnProperty을 사용하는 것이 아닌 Object 프로토타입의 hasOwnProperty 속성을 직접적으로 사용
// 'this'를 foo로 설정, hasOwnProperty의 인자는 "bar" 하여 호출
Object.prototype.hasOwnProperty.call(foo, "bar"); // true

// 다른 객체의 hasOwnProperty 사용
// 'this'를 foo로 설정, hasOwnProperty의 인자는 "bar"로 하여 호출
({}).hasOwnProperty.call(foo, "bar"); // true
```

또는 코드 스니펫을 직접 만들어 사용할 수도 있습니다.

```javascript
function hasOwnProp(targetObj, targetProp) {
  return Object.prototype.hasOwnProperty.call(targetObj, targetProp);
}

const person = {
  name: "zima",
};

hasOwnProp(person, "name"); // true
```

## 8. 직접 접근 지양

아래의 코드에서 model은 중요한 값을 가지고 있는데 그에 비해 접근이 쉽습니다.

```javascript
const model = {
  isLogin: false,
  isValidToken: false,
};

function login() {
  model.isLogin = true;
  model.isValidToken = true;
}

function logout() {
  model.isLogin = false;
  model.isValidToken = false;
}

someElement.addEventListener("click", login);
```

모델에 접근하는 권한을 함수에 위임하고 추상화한다면 안전하게 사용할 수 있습니다.
또한 로그를 사용하여 예측 가능한 코드가 될 수 있습니다.

```javascript
const model = {
  isLogin: false,
  isValidToken: false,
};

// model 객체를 직접 조작하는 영역을 따로 생성
function setLogin(bool) {
  model.isLogin = bool;
  serverAPI.log(model.isLogin);
}
function setValidToken(bool) {
  model.isValidToken = bool;
  serverAPI.log(model.isValidToken);
}

// model 객체를 직접 접근 x
function login() {
  setLogin(true);
  setValidToken(true);
}
function logout() {
  setLogin(false);
  setValidToken(false);
}

someElement.addEventListener("click", login);
```

## 9. Optional Chaining

Optional Chaining은 물음표와 마침표로 이루어져 있습니다. (`?.`)

마침표인 점 연산자는 객체를 체인으로 탐색하는 역할을 가지고 있습니다.

```javascript
const obj = {
  name: "value",
};

obj.name; // 'value'
```

만약 서버에서 데이터를 가져오는데 유실이 생기는 경우 런타임에서 에러가 발생할 수 있습니다.

아래의 코드는 `if` 문을 사용하여 데이터에 유실이 생겼을 때 `'알 수 없는 에러가 발생했습니다.'`를 반환하도록 조치하였습니다.
이 방법은 `if` 문 지옥이 발생합니다.

```javascript
const response = {
  data: {
    // userList: [
    //  {
    //     name: 'Jang',
    //     info: {
    //       tel: '010',
    //       email: 'jang@gmail.com'
    //     }
    //   }
    // ]
  },
};

// 'jang@gmail.com'

function getUserEmailByIndex(userIndex) {
  if (response.data) {
    if (response.data.userList) {
      if (response.data.userList[userIndex]) {
        return response.data.userList[userIndex].info.email;
      }
      return "알 수 없는 에러가 발생했습니다.";
    }
    return "알 수 없는 에러가 발생했습니다.";
  }
  return "알 수 없는 에러가 발생했습니다.";
}

getUserEmailByIndex(0);
```

`if` 문의 조건에 `&&` 를 사용하여 덜 복잡하게 할 수 있지만 더 좋은 방법이 있습니다.

Optional Chaining을 사용하면 불필요한 `if` 문 없이도 중첩된 데이터가 존재하는지 여부를 확인할 수 있습니다.

```javascript
function getUserEmailByIndex(userIndex) {
  if (response?.data?.userList[userIndex]?.info?.email) {
    return response.data.userList[userIndex].info.email;
  }
  return "알 수 없는 에러가 발생했습니다.";
}
getUserEmailByIndex(0);
```

## 참고

[클린코드 자바스크립트(JavaScript)](https://www.udemy.com/course/clean-code-js/)
