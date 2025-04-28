# 클린코드 for 자바스크립트(분기)

## 1. 값, 식, 문

함수의 매개변수에 `if`문, `for`문 등을 넣을 수 있을까요?
값과 식과 문이 사용될 수 있는 곳과 사용할 수 없는 곳에 대한 글입니다.

### 예시 1

리엑트에 작성하는 JSX 코드는 Babel을 통해 자바스크립트로 변환되어 실행됩니다.

```jsx
// JSX

ReactDOM.render(<div id="msg">Hello World!</div>, mountNode);
```

위의 리엑트 코드는 아래의 자바스크립트와 같습니다.

```js
// transformed to JS

ReactDOM.render(
  React.createElement("div", { id: "msg" }, "Hello World!"),
  mountNode
);
```

#### 값의 자리에 문이 올 수 있는가

가끔 어떤 이는 리엑트에서 아래와 같은 코드를 작성할 때가 있습니다.

```jsx
// JSX

<div id={if (condition) { 'msg' }}>Hello World!</div>
```

위의 코드를 자바스크립트로 변환하면 아래와 같습니다.

```js
// transformed to JS

React.createElement("div", {id: if (condition) { 'msg' }}, "Hello World!");
```

위의 코드를 보면 객체의 **_값_** 의 자리에 조건**_문_** 을 사용한 것으로 자바스크립트로 변환하여 보니 굉장히 이질적입니다.

#### 값의 자리에 식이 올 수 있는가

`if`문 대신 아래와 같이 삼항연산자를 사용하면 문제가 되지 않습니다.

```js
// JSX

ReactDOM.render(<div id={condition ? "msg" : null}>Hello</div>);
```

삼항연산자는 표현식이고 **_식_** 은 **_값_** 으로 귀결될 수 있습니다.
따라서 값의 위치에 식이 올 수 있는 것 입니다.

### 예시 2

#### 객체에 문을 사용하는 방법

아래의 코드에서는 객체 중괄호(`{}`) 내부에 `switch` **_문_** 이 들어있습니다.
객체에는 값과 식만 사용될 수 있다고 했습니다.
하지만 정의되자마자 즉시 실행되는 IIFE(즉시실행함수)를 사용하여 값을 바로 `return` 하기 때문에 `switch` **_문_** 을 사용할 수 있게 됩니다.

```jsx
// JSX

<p>
  {(() => {
    switch (this.state.color) {
      case "red":
        return "#FF0000";
      case "green":
        return "#00FF00";
      case "blue":
        return "#0000FF";
      default:
        return "#FFFFFF";
    }
  })()}
</p>
```

### 예시 3

즉시실행함수를 사용하여 for **_문_** 을 객체에 사용했습니다.
rows 배열에 값을 누적하여 저장했다가 반환하는 방식을 사용하였는데, 더 좋은 방법이 있습니다.

```jsx
// JSX

function ReactComponent() {
  return (
    <tbody>
      {(() => {
        const rows = [];

        for (let i = 0; i < objectRows.length; i++) {
          rows.push(<ObjectRow key={i} data={objectRows[i]} />);
        }

        return rows;
      })()}
    </tbody>
  );
}
```

함수를 인자로 전달받거나 함수를 결과로 반환 고차함수를 사용하면 값을 누적하지 않고 바로 반환하여 렌더링할 수 있습니다.

```jsx
// JSX

function ReactComponent() {
  return (
    <tbody>
      {objectRows.map((obj, i) => (
        <ObjectRow key={i} data={objectRows[i]} />
      ))}
    </tbody>
  );
}
```

## 2. 삼항 연산자

삼항 연산자는 3개의 피연산자를 취하며 숏코딩에 도움이 됩니다.
조건에 따른 참과 거짓에는 식이 들어가야 합니다.

```js
조건 ? 참 : 거짓;
```

### 중복된 삼항 연산자

삼항 연산자를 중복하여 사용할 때는 다른 사람이 햇갈리지 않게 소괄호로 감싸줍니다.

#### 소괄호 미사용

```js
const example = condition1
  ? a === 0 ? 'zero' : 'positive'
  : a 'negative'
```

#### 소괄호 사용

```js
const example = condition1
  ? (a === 0 ? 'zero' : 'positive')
  : a 'negative'
```

### 삼항 연산자가 잘못 사용된 예시

아래의 함수는 여러 조건에 따른 여러 분기를 가지고 있습니다.
같은 일을 하지만 조건이 복잡한 경우 `switch` 문이 더 직관성과 일관성을 가지고 있습니다.

#### 예시1

```js
// 삼항 연산자

function example() {
  return condition1
    ? value2
    : condition2
    ? value2
    : condition3
    ? value3
    : value4;
}
```

```js
// if 문

function example() {
  if (condition1) {
    return value1;
  } else if (condition2) {
    return value2;
  } else if (condition3) {
    return value3;
  } else {
    return value4;
  }
}
```

```js
// switch 문

function example() {
  switch (condition) {
    case condition1:
      return value1;
    case condition2:
      return value2;
    case condition3:
      return value3;
    default:
      return value4;
  }
}
```

#### 예시2

`alert()`은 반환값이 `void`입니다.
`void`는 아무것도 반환하지 않는 함수들이 갖는 반환 타입입니다.
결국 아래의 삼항연산자의 참과 거짓 모두 `undefined`를 갖게 됩니다.

```js
// 삼항 연산자

function alertMessage(isAdult) {
  isAdult ? alert("입장이 가능합니다.") : alert("입장이 불가능합니다.");
}
```

```js
// if 문

function alertMessage(isAdult) {
  if (isAdult) {
    alert("입장이 가능합니다.");
  } else {
    alert("입장이 불가능합니다.");
  }
}
```

### 삼항 연산자를 잘 사용한 예시

#### 삼항 연산자를 잘 사용한 예시1

값이 null, 빈값이 필요할 수도 있을 때 사용하기 좋습니다.
단, 의도된 빈값이 아닌 참 또는 거짓 하나만 필요한 경우에는 Truthy & Falsy를 사용해야 합니다.

```js
const welcomeMessage = (isLogin) => {
  if (isLogin) {
    return `안녕하세요 ${getName()}`;
  }
  return `안녕하세요 이름없음`;
};
```

```js
const welcomeMessage = (isLogin) => {
  const name = isLogin ? getName() : "이름없음";
  return `안녕하세요 ${name}`;
};
```

#### 삼항 연산자를 잘 사용한 예시2

값을 반환하는 간단한 경우에도 사용하기 좋습니다.

```js
function alertMessage(isAdult) {
  const isAdult ? '입장이 가능합니다.' : '입장이 불가능합니다';
}
```

```js
function alertMessage(isAdult) {
  return isAdult ? "입장이 가능합니다." : "입장이 불가능합니다";
}
```

## 3. Truthy & Falsy

Truthy & Falsy를 잘 사용하면 코드를 간단하게 만들 수 있습니다.

### Truthy

참 같은 값(Truthy)인 값이란 불리언을 기대하는 문맥에서 true로 평가되는 값입니다.

```js
if (true)

if ({})

if ([])

if (42)

if ("0")

if ("false")

if (new Date())

if (-42)

if (12n)

if (3.14)

if (-3.14)

if (Infinity)

if (-Infinity)
```

### falsy

거짓 같은 값(Falsy, falsey) 값은 Boolean 문맥에서 false로 평가되는 값입니다.

```js
if (false) {
  // 도달할 수 없습니다.
}

if (null) {
  // 도달할 수 없습니다.
}

if (undefined) {
  // 도달할 수 없습니다.
}

if (0) {
  // 도달할 수 없습니다.
}

if (-0) {
  // 도달할 수 없습니다.
}

if (0n) {
  // 도달할 수 없습니다.
}

if (NaN) {
  // 도달할 수 없습니다.
}

if ("") {
  // 도달할 수 없습니다.
}
```

## 4. 단축평가 (short-circuit evaluation)

### 첫 번째 falsy를 찾는 AND 연산자 ‘&&’

값이 `false`이면 평가를 멈추고 해당 피연산자의 변환 전 원래 값을 반환합니다.
모든 피연산자가 `true`로 평가되는 경우엔 마지막 피연산자가 반환됩니다.

```js
/**
 * AND
 */

true && true && "도달 O";

true && false && "도달 X";
```

### 첫 번째 truthy를 찾는 OR 연산자 ‘||’

값이 `true`이면 연산을 멈추고 해당 피연산자의 변환 전 원래 값을 반환합니다.
모든 피연산자가 `false`로 평가되는 경우엔 마지막 피연산자를 반환합니다.

```js
/**
 * OR
 */

false || false || "도달 O";

true || true || "도달 X";
```

### 사용 예시

```js
// if 문

function fetchData(state) {
  if (state.data) {
    return state.data;
  } else {
    return "Fetching...";
  }
}
```

```js
// 삼항 연산자

function fetchData(state) {
  return state.data ? state.data : "Fetching...";
}
```

```js
// OR 단축평가

function fetchData(state) {
  return state.data || "Fetching...";
}
```

```js
// if 문

function favoriteDog(someDog) {
  let favoriteDog;
  if (someDog) {
    favoriteDog = dog;
  } else {
    favoriteDog = '냐옹';
  }
  return favorite Dog + '입니다';
}
```

```js
// OR 단축평가

function favoriteDog(someDog) {
  return (someDog || "냐옹") + "입니다";
}
```

```js
// 중복된 if 문

const getActiveUserName(user, isLogin) {
  if (isLogin) {
    if (user) {
      if (user.name) {
        return user.name
      } else {
        return '이름없음'
      }
    }
  }
}
```

```js
// 단축평가

const getActiveUserName(user, isLogin) {
  if (isLogin && user) {
    return user.name || '이름없음'
  }
}
```

## 5. else if 피하기

`else if`문은 `else` 안의 `if`문과 같습니다.
결국 `if`문을 두 번 사용한 것과 같은데, `if`문을 두 번 사용한 것이 더 명확합니다.

```js
// else if 두 번

if (x >= 0) {
  console.log("x는 0과 같거나 크다");
} else if (x > 0) {
  console.log("x는 0보다 크다");
}
```

```js
// else 안의 if

if (x >= 0) {
  console.log("x는 0과 같거나 크다");
} else {
  if (x > 0) {
    console.log("x는 0보다 크다");
  }
}
```

```js
// if 두 번

if (x >= 0) {
  console.log("x는 0과 같거나 크다");
}
if (x > 0) {
  console.log("x는 0보다 크다");
}
```

만약 `else if` 문을 많이 사용하는 경우라면 `switch`문을 사용하는 것이 좋습니다.

## 6. else 피하기

아래의 함수는 같은 환경입니다.

```js
function getActiveUserName(user) {
  if (user.name) {
    return user.name;
  } else {
    return "이름없음";
  }
}
```

하지만 아래의 환경이 더 명확합니다.

```js
function getActiveUserName(user) {
  if (user.name) {
    return user.name;
  }
  return "이름없음";
}
```

## 7. Early Return

함수를 미리 종료하면 사고하기 편합니다.

아래 두 함수는 같습니다.

```js
function loginService(isLogin, user) {
  if (!isLogin) {
    // 로그인 여부
    if (checkToken()) {
      // 토큰 존재
      if (!user.nickName) {
        // 기가입 유저 확인
        return registerUser(user); // 기가입 아니면 가입
      } else {
        refreshToken();
        return "로그인 성공"; // 기가입 이라면 로그인 성공
      }
    } else {
      throw new Error("No Token");
    }
  }
}
```

```js
// Early Return

function loginService(isLogin, user) {
  if (isLogin) {
    // 로그인 여부
    return;
  }

  if (!checkToken()) {
    // 토큰 존재
    throw new Error("No Token");
  }

  if (!user.nickName) {
    // 기가입 유저 확인
    return registerUser(user); // 기가입 아니면 가입
  }

  refreshToken();
  return "로그인 성공"; // 기가입 이라면 로그인 성공
}
```

## 8. 부정 조건문 지양

부정 조건문을 사용하면 생각을 여러번 해야합니다.
유효성 검증 혹은 Early Return과 같이 검사 해야하는 로직을 제외하면 부정 조건문을 지양하는 것이 실수를 줄일 수 있습니다.

```js
if (!isCondition) {
  console.log("거짓인 경우에만 실행");
}
```

```js
if (isNotCondition) {
  console.log("거짓인 경우에만 실행");
}
```

## 9. Default Case 고려

Default Case를 정해 놓는다면 함수 인자에 값이 없더라도 안전하게 사용할 수 있습니다.

### or 단축평가

```js
function sum(x, y) {
  x = x || 1; // Default Case
  y = y || 1; // Default Case
  return x + y;
}

sum();
```

### switch default

```js
function registerDay(userInputDay) {
  switch (userInputDay) {
    case "월요일":
    case "화요일":
    case "수요일":
    case "목요일":
    case "금요일":
    case "토요일":
    case "일요일":
    default: // Default Case
      throw Error("입력값이 유효하지 않습니다.");
  }
}

registerDay("월ㄹ요일");
```

### react switch

```jsx
const Root = () => {
  <Router>
    <Switch>
      <Route exact path="/" component={App} />
      <Route path="/welcome" component={welcome} />
      <Route component={NotFound} /> // Default Case
    </Switch>
  </Router>;
};
```

### parseInt()

```js
function safeParseInt(number, radix) {
  return parseInt(number, radix || 10);
}
```

## 10. 명시적인 연산자 사용 지향

괄호를 사용하여 연산자 우선순위를 사람이 편하게 볼수 있도록 하는 것이 좋습니다.

```js
if ((isLogin && token) || user) {
}
if ((isLogin && token) || user) {
}
```

증감연산자는 전위인지, 후위인지에 따라 예측하기 쉽지 않기 때문에 실수가 발생할 수 있습니다.

```js
function decrease(number) {
  --number;
  number--;
}
function increment(number) {
  --number;
  number--;
}
```

따라서 증감연산자보다는 명확하게 1을 더해주는 코드를 사용하는 것이 좋습니다.

```js
function decrement(number) {
  number = number + 1;
  number += 1;
}
function increment(number) {
  number = number - 1;
  number -= 1;
}
```

## 11. Nullish coalescing operator

falsy에 해당되는 값을 다룰 때 값으로서 판단하고 싶지만 그럴 수 없는 경우가 있습니다.

### 예시

`height`와 `width`를 0px로 하고 싶지만 숫자 0은 falsy에 해당하기 때문에 Default Case인 10이 해당되게 됩니다.

```js
function createElement(type, height, width) {
  const element = document.createElement(type || "div");
  element.style.height = String(height || 10) + "px";
  element.style.width = String(width || 10) + "px";
  return element;
}

const el = createElement("div", 0, 0);

el.style.height; // '10px'
el.style.width; // '10px'
```

이러한 문제점을 해결하기 위해 사용하는 것이 null 병합 연산자입니다.(`??`)
null 병합 연산자는 좌측 값이 `null` 또는 `undefined`일 때만 동작합니다.

```js
function createElement(type, height, width) {
  const element = document.createElement(type ?? "div");
  element.style.height = String(height ?? 10) + "px";
  element.style.width = String(width ?? 10) + "px";
  return element;
}

const el = createElement("div", 0, 0);

el.style.height; // '0px'
el.style.width; // '0px'
```

null 병합 연산자와 or연산자를 함께 사용하면 에러가 발생합니다.
따라서 함께 사용할 때에는 소괄호를 통해 분리해주어야 합니다.

```js
console.log(null || undefined ?? 'foo')
// SyntaxError

console.log((null || undefined) ?? 'foo')
// 'foo'
```

## 12. 드모르간의 법칙

로그인 성공여부를 확인하는 검증된 코드가 있다고 생각해보겠습니다.

```js
const isValidUser = true; // 서버에서 받은 값
const isValidToken = true; // 서버에서 받은 값

if (isValidToken && isValidUser) {
  console.log("로그인 성공");
}
```

이후 로그인 실패를 확인하는 코드를 추가하기 위해서는 검증된 코드를 활용하게 됩니다.

```js
const isValidUser = false; // 서버에서 받은 값
const isValidToken = false; // 서버에서 받은 값

if (!(isValidToken && isValidUser)) {
  console.log("로그인 실패");
}
```

하지만 추가로 기획이 요구된다면 복잡할 수 있습니다.

```js
const isValidUser = false; // 서버에서 받은 값
const isValidToken = false; // 서버에서 받은 값

if (!(isValidToken && isValidUser) && some || some...) {
  console.log('로그인 실패');
}
```

이럴때 드모르간의 법칙을 생각하면 도움이 됩니다.

> true is not false
> false is not true

아래의 두 조건은 같습니다.

```js
if (!(A && B)) {
  // 성공
}

if (!A || !B) {
  // 성공
}
```

따라서 로그인 실패를 확인하는 코드를 추가할 때 아래와 같이 만들 수 있습니다.

```js
const isValidUser = true; // 서버에서 받은 값
const isValidToken = true; // 서버에서 받은 값

if (isValidToken && isValidUser) {
  console.log("로그인 성공");
}
```

```js
const isValidUser = false; // 서버에서 받은 값
const isValidToken = false; // 서버에서 받은 값

if (!isValidToken || !isValidUser) {
  console.log("로그인 실패");
}
```

## 참고

[클린코드 자바스크립트(js)](https://www.udemy.com/course/clean-code-js/)
