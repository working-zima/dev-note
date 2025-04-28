# 클린코드 for 자바스크립트(배열)

## 1. Javascript의 배열은 객체

배열과 객체를 단순히 비교하더라도 비슷합니다.

```javascript
const arr = [1, 2, 3];

arr[3] = "test";
arr["property"] = "string value";
arr["obj"] = {};
arr[{}] = [1, 2, 3];
arr["func"] = function () {
  return "hello";
};

// [1, 2, 3, 'test', property: 'string value', obj: {…}, [object Object]: Array(3), func: ƒ]
```

```javascript
const obj = {
  arr: [1, 2, 3],
  3: "test",
  property: "string value",
  obj: {},
  "{}": [1, 2, 3],
  func: function () {
    return "hello";
  },
};
// {3: 'test', arr: [1, 2, 3], property: 'string value', obj: {…}, {}: Array(3), func: ƒ}
```

배열은 index를 키로 가진 객체라고 생각하면 다른게 없습니다.

```javascript
const arr = [1, 2, 3];
const obj = { 0: 1, 1: 2, 2: 3 };
```

## 2. Array.length

배열의 length는 길이를 보장하지 않습니다.

```javascript
const arr = [1, 2, 3];
arr.length = 10;

arr; // [1, 2, 3, empty x7]
```

길이보다는 마지막 인덱스를 알려주는 것에 가깝습니다.

```javascript
const arr = [1, 2, 3];
arr[9] = 4;

arr.length; // 10
arr; // [1, 2, 3, empty x 6, 4]
```

이러한 점을 이용해서 배열을 값을 모두 제거될 수 있습니다.

```javascript
function clearArray(array) {
  array.length = 0;
  return array;
}

const arr = [1, 2, 3];
clearArray(arr); // []
```

따라서 배열의 length는 주의해서 사용해야 합니다.

## 3. 배열 요소에 접근

`[]`을 사용해 배열의 요소에 접근할 때 어떤 값인지 알기 힘듭니다.

### 예시1

```javascript
function operateTime(input, operators, is) {
  input[0].split("").forEach((num) => {
    cy.get(".digit").contains(num).click();
  });
  input[1].split("").forEach((num) => {
    cy.get(".digit").contains(num).click();
  });
}
```

구조분해할당을 사용하면 명시적으로 요소를 사용할 수 있습니다.

```javascript
function operateTime(input, operators, is) {
  const [firstInput, secondInput] = input;
  firstInput.split("").forEach((num) => {
    cy.get(".digit").contains(num).click();
  });
  secondInput.split("").forEach((num) => {
    cy.get(".digit").contains(num).click();
  });
}
```

인자에 사용한다면 더 간단하게 사용할 수 있습니다.

```javascript
function operateTime([firstInput, secondInput], operators, is) {
  firstInput.split("").forEach((num) => {
    cy.get(".digit").contains(num).click();
  });
  secondInput.split("").forEach((num) => {
    cy.get(".digit").contains(num).click();
  });
}
```

### 예시2

```javascript
function clickGroupButton() {
  const confirmButton = document.getElementsByTagName("button")[0];
  const cancelButton = document.getElementsByTagName("button")[1];
  const resetButton = document.getElementsByTagName("button")[2];
}
```

```javascript
function clickGroupButton() {
  const [confirmButton, cancelButton, resetButton] =
    document.getElementsByTagName("button");
}
```

### 예시3

```javascript
function formatData(targetDate) {
  const date = targetDate.toISOString().split("T")[0];
  const [year, month, day] = date.split("-");
  return `${year}년 ${month}월 ${day}일`;
}
```

```javascript
function formatData(targetDate) {
  const [date] = targetDate.toISOString().split("T");
  const [year, month, day] = date.split("-");
  return `${year}년 ${month}월 ${day}일`;
}
```

예시와 같이 배열의 요소에 접근할 때 인덱스를 통해 접근하는 것을 줄이면 가독성을 높일 수 있습니다.

## 4. 유사 배열 객체

### 배열을 흉내낸 객체

```javascript
const arrayLikeObject = {
  0: "HELLO",
  1: "WORLD",
  length: 2,
};

const arr = Array.from(arrayLikeObject);
console.log(arr); // [ 'HELLO', 'WORLD' ]
console.log(arr.length); // 2

console.log(Array.isArray(arrayLikeObject)); // false
```

### arguments

```javascript
function generatePriceList() {
  console.log(Array.isArray(arguments)); // false

  for (let i = 0; i < arguments.length; i++) {
    const element = arguments[i];
    console.log(element); // 100, 200, 300, 400, 500
  }
}
generatePriceList(100, 200, 300, 400, 500);
```

## 5. 불변성

### 배열을 복사

`newArray`에 `originArray`을 초기화 하여 복사했습니다.
원본 배열 `originArray`에 값을 추가할 때 새로운 배열 `newArray`에도 동일하게 추가 됩니다.

```javascript
const originArray = ["123", "456", "789"];

const newArray = originArray;

originArray.push(10);
originArray.push(11);
originArray.push(12);
originArray.unshift(0);

originArray; // [0, '123', '456', '789', 10, 11, 12]
newArray; // [0, '123', '456', '789', 10, 11, 12]
```

반대로 새로운 배열 `newArray`를 조작해도 원본 배열 `originArray`이 영향을 받습니다.

```javascript
const originArray = ["123", "456", "789"];

const newArray = originArray;

newArray.push(10);
newArray.push(11);
newArray.push(12);
newArray.unshift(0);

originArray; // [0, '123', '456', '789', 10, 11, 12]
newArray; // [0, '123', '456', '789', 10, 11, 12]
```

이런 경우 많은 문제가 생길 수 있습니다.

구조 분해 할당을 사용하여 `newArray`에 `originArray`을 복사하게 된다면 `originArray`에 값을 추가해도 `newArray`에는 추가되지 않습니다.

```javascript
const originArray = ["123", "456", "789"];

const newArray = [...originArray];

originArray.push(10);
originArray.push(11);
originArray.push(12);
originArray.unshift(0);

originArray; // [0, '123', '456', '789', 10, 11, 12]
newArray; // ['123', '456', '789']
```

### 새로운 배열을 반환하는 메서드를 활용

이외에도 `filter`, `map`, `slice` 등은 새로운 배열을 반환하여 원본 배열에 영향을 받지 않습니다.

## 6. for 문 배열 고차 함수 리팩터링

for 문을 사용하면 명시적이지 않은 임시변수 `temp`와 `i`를 사용하게 됩니다.

```javascript
const price = ["2000", "1000", "3000", "5000", "4000"];

function getWonPrice(priceList) {
  let temp = [];
  for (let i = 0; i < priceList.length; i++) {
    temp.push(priceList[i] + "원");
  }
  return temp;
}

getWonPrice(price); // [ '2000원', '1000원', '3000원', '5000원', '4000원' ]
```

고차 함수 `map`을 사용하면 불필요한 임시 변수를 사용하지 않아 코드가 더욱 명시적으로 변합니다.

```javascript
const price = ["2000", "1000", "3000", "5000", "4000"];

function getWonPrice(priceList) {
  return priceList.map((price) => price + "원");
}

getWonPrice(price); // [ '2000원', '1000원', '3000원', '5000원', '4000원' ]
```

고차 함수를 사용하면 추가 요구사항이 있어도 명확하고 선언적으로 수정할 수 있습니다.

```javascript
const price = ["2000", "1000", "3000", "5000", "4000"];

const suffixWon = (price) => price + "원";
const isOverOneThousand = (price) => Number(price) > 1000;
const ascendingList = (a, b) => a - b;

function getWonPrice(priceList) {
  const isOverList = priceList.filter(isOverOneThousand);
  const sortList = isOverList.sort(ascendingList);
  return sortList.map(suffixWon);
}

getWonPrice(price); // [ '2000원', '3000원', '4000원', '5000원' ]
```

## 7. 메서드 체이닝

메서드 체이닝을 활용하면 코드의 진행이 명확한 파이프 라인처럼 보일 수 있습니다.

```javascript
const price = ["2000", "1000", "3000", "5000", "4000"];

const suffixWon = (price) => price + "원";
const isOverOneThousand = (price) => Number(price) > 1000;
const ascendingList = (a, b) => a - b;

function getWonPrice(priceList) {
  return priceList.filter(isOverOneThousand).sort(ascendingList).map(suffixWon);
}

getWonPrice(price); // [ '2000원', '3000원', '4000원', '5000원' ]
```

## 8. map vs forEach

`map`은 `return` 으로 새로운 배열을 반환하고,
`forEach`는 `return` 으로 `undefined`를 반환합니다.

## 9. Continue vs Break

고차 함수에서는 `continue`와 `break`를 사용할 수 없습니다.

```javascript
const orders = ['first', 'second', 'third']

orders.forEach(function(order) {
  if(order === 'second') {
    break;
  }
  console.log(order); // SyntaxError: Unsyntactic break
})
```

```javascript
const orders = ['first', 'second', 'third']

orders.forEach(function(order) {
  if(order === 'second') {
    continue;
  }
  console.log(order); // SyntaxError: Unsyntactic continue
})
```

`try`/`catch` 구문을 사용하여 해결할 수 있습니다.

```javascript
const orders = ['first', 'second', 'third']

try {
  orders.forEach(function(order) {
    if(order === 'second') {
      throw;
    }
    console.log(order); // first
})
} catch (e) {}
```

하지만 `for...in` 또는 `for...of` 문을 사용하는 것이 좋습니다.
또한 `every`, `some`, `find` 등을 통해 흐름을 제어해서 조기 종료를 할 수도 있습니다.

## 참고

[클린코드 자바스크립트(JavaScript)](https://www.udemy.com/course/clean-code-js/)
