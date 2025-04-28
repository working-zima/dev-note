# 클린코드 for 자바스크립트(변수)

## 1. var 지양

### var

var은 함수 스코프를 갖습니다.
if문은 함수가 아니기 때문에 if문에서 var로 선언한 변수가 if문의 밖으로도 영향을 줍니다.

```javascript
var global = "전역";

if (global === "전역") {
  var global = "지역";

  console.log(global); // 지역
}

console.log(global); // 지역
```

var은 재선언이 가능하며 가장 마지막에 선언된 변수를 갖게 됩니다.

```javascript
console.log(name); //undefined

var name = "이름";
var name = "이름2";
var name = "이름3";

console.log(name); // '이름3'
```

### let, const

let과 const 블록 스코프를 갖습니다.
if문은 블록이기 때문에 if문에서 선언한 변수가 외부에 영향을 주지 않습니다.

```javascript
let global = "전역";

if (global === "전역") {
  let global = "지역";

  console.log(global); // 지역
}

console.log(global); // 전역
```

var과 다르게 재할당을 할 수 없기 때문에 선언해 놓은 변수가 재선언될 위험이 없습니다.

```javascript
let name = "이름";
let name = "이름2"; // SyntaxError
```

#### const

const는 상수를 선언하며 let과 다르게 재할당이 불가능합니다.
따라서 const를 사용할 때 가장 안전하게 값을 관리할 수 있습니다.

```javascript
const person = {
  name: "jang",
  age: 30,
};

person = {
  // Assignment to constant variable
  name: "jang2",
  age: 30,
};
```

## 2. 전역 공간 사용 최소화

### 전역공간이란

최상위 공간을 말하며, 브라우저 환경이라면 window, NodeJS 환경이라면 global을 말합니다.

### 전역 공간을 사용할 때 문제점

자바스크립트는 파일을 나누어도 스코프가 나뉘지 않습니다.
따라서 전역공간에 영향을 주었을 때 모든 페이지에 적용이 됩니다.
따라서 모든 페이지를 정확하게 암기하지 않으면 문제가 생길 가능성도 크며 문제를 파악하는 것도 힘들 수 있습니다.

```javascript
// index1.js

var globalVar = "global";

console.log(globalVar); // 'global'
console.log(window.globalvar); // 'global'

var setTimeout = "setTimeout";
```

```javascript
// index2.js

console.log(window.globalvar); // 'global'

window.setTimeout(() => {
  // Error
  console.log("1초");
}, 1000);
```

var는 전역 공간을 사용하는 코드입니다.
반복문에서 let이 아닌 var를 사용할 경우입니다.

```javascript
const array = [10, 20, 30];

for (var index = 0; index < array.length; index++) {
  const element = array[index];
}

console.log(window.index); // 3
```

### 어떻게 전역 공간 사용을 줄일 수 있을까

1. 전역 변수 대신 지역 변수를 사용합니다.

2. Window, Global에 접근하여 조작하지 않습니다.
3. var 대신 const, let를 사용합니다.
4. IIFE, Module, Closure 로 스코프를 나누어 사용합니다.

## 3. 임시변수 제거

임시변수는 어느 특정 공간에 Scope 안에서 전역변수처럼 활용되는 것입니다.

임시변수를 사용하게 되면 아래와 같은 문제가 생겨납니다.

1. 명령형으로 가득한 로직

2. 복잡한 코드 확장

3. 추가적인 코드 작성 유혹

4. 어려운 디버깅

### 해결책

이에 대한 해결책으로는 아래와 같은 방법이 있습니다.

1. 함수 나누기

2. 즉시 반환

3. 고차 함수 사용(map, filter, reduce 등)

4. 선언형 프로그래밍

### 예시 1 (코드의 명확성)

아래의 함수의 생명주기는 DOM을 가져온 뒤 객체에 넣어주고 반환하면 종료 됩니다.

```javascript
function getElements() {
  const result = {}; // 임시 객체

  result.title = document.querySelector(".title");
  result.text = document.querySelector(".text");
  result.value = document.querySelector(".value");

  return result;
}
```

위의 코드는 아래처럼 사용할 수 있습니다.

```javascript
function getElements() {
  return {
    title: document.querySelector(".title"),
    text: document.querySelector(".text"),
    value: document.querySelector(".value"),
  };
}
```

이렇게 코드를 사용하면 임시 객체에 접근하는 과정을 생략하면서 코드가 명확해지고 적은 사이드 이펙트를 갖게 됩니다.

### 예시 2 (코드의 확장)

```javascript
function getDateTime(targetDate) {
  let month = targetDate.getMonth();
  let day = targetDate.getDate();
  let hour = targetDate.Hours();

  month = month >= 10 ? month : "0" + month;
  day = day >= 10 ? day : "0" + day;
  hour = hour >= 10 ? hour : "0" + hour;

  return { month, day, hour };
}
```

let은 수정이나 재할당을 약속하는 의미일 수 있기 때문에 재할당 하지 않을 것이라면 const를 사용합니다.
반환할 값도 재할당 없이 바로 반환해줍니다.

```javascript
function getDateTime(targetDate) {
  const month = targetDate.getMonth();
  const day = targetDate.getDate();
  const hour = targetDate.Hours();

  return {
    month: month >= 10 ? month : "0" + month,
    day: day >= 10 ? day : "0" + day,
    hour: hour >= 10 ? hour : "0" + hour,
  };
}
```

기존 함수에 추가적인 기능이 필요할 때, 함수가 명확하다면 기존 함수를 활용하여 추가 기능 구현이 용이합니다.

```javascript
function getDateTime(targetDate) {
  const month = targetDate.getMonth();
  const day = targetDate.getDate();
  const hour = targetDate.getHours();

  return {
    month: month >= 10 ? month : "0" + month,
    day: day >= 10 ? day : "0" + day,
    hour: hour >= 10 ? hour : "0" + hour,
  };
}

function drawDateTime() {
  const currentDateTime = getDateTime(new Date());

  return {
    month: currentDateTime.month + "분 전",
    day: currentDateTime.day + "일 전",
    hour: currentDateTime.hour + "시간 전",
  };
}
```

### 예시 3 (조작 가능성)

```javascript
function genRandomNumber(min, max) {
  let randomNumber = Math.floor(Math.random() * (max + 1) + min);

  // 조작 가능성

  return randomNumber;
}
```

위의 코드는 조작될 수 있는 임시변수가 있고 임시변수를 조작할 수 있는 공간도 있습니다.
하지만 아래의 함수는 코드의 목적을 달성 후 반환값을 바로 반환했기 때문에 임시변수 조작의 유혹에서 벗어날 수 있습니다.

```javascript
function genRandomNumber(min, max) {
  return Math.floor(Math.random() * (max + 1) + min);
}
```

### 예시 4 (어려운 예측)

임시 변수가 계속 바뀌기 때문에 반환값을 예측하기 힘듭니다.

```javascript
function getSomeValue(params) {
  let array = params;
  let temp = 1;

  for (let index = 0; index < array.length; index++) {
    temp *= array[index];
    temp += array[index];
    temp /= array[index];
    temp -= array[index];
  }

  if (temp <= 30) temp += 40;
  else temp *= 2;

  return temp;
}
```

## 4. 호이스팅 주의

호이스팅은 런타임 시기에 선언과 할당이 분리되는 현상을 말합니다.
호이스팅은 코드작성 순서와 다르게 동작하기 때문에 예측하지 못한 결과가 발생할 수 있습니다.

해결법으로는 아래와 같은 방법이 있습니다.

1. var 사용 금지

2. 함수 표현식 사용

### 예시 1

var는 함수 스코프이기 때문에 `outer`함수의 첫 `console.log(global)`가 0으로 예상했으나 undefined가 출력됩니다.

```javascript
var global = 0;

function outer() {
  console.log(global); // undefined
  var global = 5;

  function inner() {
    var global = 10;
    console.log(global); // 10
  }
  inner();
  global = 1;
  console.log(global); // 1
}
```

이유는 실제 런타임에서는 선언과 할당이 분리되기 때문입니다.

```javascript
var global = 0;

function outer() {
  var global; // 선언
  console.log(global); // undefined
  global = 5; // 할당

  function inner() {
    var global = 10;
    console.log(global); // 10
  }
  inner();
  global = 1;
  console.log(global); // 1
}
```

### 예시 2

함수도 호이스팅 됩니다.

```javascript
var sum;

console.log(typeof sum); // function

function sum() {
  return 1 + 2;
}
```

const에 함수를 할당하는 방식(함수 표현식)을 사용하면 해결할 수 있습니다.

```javascript
var sum;

console.log(typeof sum); // SyntaxError: Identifier 'sum' has already been declared

const sum = function () {
  return 1 + 2;
};
```

```javascript
console.log(typeof sum); // ReferenceError: Cannot access 'sum' before initialization

const sum = function () {
  return 1 + 2;
};
```

```javascript
const sum = function () {
  return 1 + 2;
};

console.log(typeof sum); // function
```

## 참고

[클린코드 자바스크립트(JavaScript)](https://www.udemy.com/course/clean-code-js/)
