# 클린코드 for 자바스크립트(경계)

흔히 사용되는 네이밍에 어떤 뉘앙스가 들어있는지에 관한 내용입니다.

## min - max

최소와 최대 값을 다룰 때 경계 숫자를 포함을 할지 안할지 여부를 컨벤션을 정하는 것이 중요합니다.
(이상, 이하/ 초과, 미만)

### 해결 예시

네이밍에 최소, 최대값 포함 여부를 표현하는 것도 방법이 될 수 있습니다.

#### 네이밍 예시 1

```javascript
function genRandomNumber(min, max) {
  return Math.floor(Math.random() * (max - min)) + min;
}

const MIN_NUMBER = 1;
// 미만, 초과 뉘앙스를 풍기는 네이밍
const MAX_NUMBER_LIMIT = 3;

genRandomNumber(MIN_NUMBER, MAX_NUMBER_LIMIT); // 1 ~ 2
```

#### 네이밍 예시 2

```javascript
// 이하, 이상 뉘앙스를 풍기는 네이밍
const MAX_IN_AGE = 20;

function isAdult(age) {
  if (age >= 20) {
    return age;
  }
}
```

## begin - end

여러 사람들과의 작업을 할 때는 시작 날짜, 마감 날짜 등에 관한 컨벤션도 필요합니다.
`begin`과 `end`를 사용할 때 보통 `begin`을 먼저 받고 `end`는 뒤에 써서 제어하도록 합니다.
`begin`이 필수 값이 되어 `begin`이 없다면 `end`도 사용할 수 없도록 하는 것입니다.

```javascript
function reservationDate(beginDate, endDate) {}

// 함수의 매개변수 순서를 왼쪽이 시작일, 오른쪽이 마감일로 하여
reservationDate("YYYY-MM-DD", "YYYY-MM-DD");

// 값이 하나라면 endDate에는 값을 전달하지 못함
reservationDate("YYYY-MM-DD");
```

## first - last

`Min`과 `Max`와 다르게 연속성과 규칙성이 없는 경우 `first`와 `last`를 사용합니다.
`Min`이 1이고 `Max`가 3일 때 1부터 2를 포함한 3까지라는 뉘앙스가 있지만,
`first`가 1이고 `last`가 3이라면 2를 포함하지 않는 1과 3이라는 뉘앙스가 있습니다.

```javascript
const students = ["1", "2", "3"];

function getStudents(first, last) {}

getStudents(students[0], students[2]);
```

## prefix - suffix

개발자들이 자주 사용하는 접두사와 접미사가 있습니다.
`get`을 사용하면 값을 가져오는 것, `set`은 값을 변경하거나 초기화하는것

이외에도 라이브러리 관점에서 리엑트에서 `use`가 붙는 다면 Hook을, `$`를 사용하면 jQuery를, `_`는 lodash를 떠올리게 됩니다.

자바스크립트에는 `#` 클래스 필드가 있습니다.

이외에도 여러 접두사와 접미사가 있으며 이런 규칙들을 알고 사용하는 것은 코드의 일관성을 가질 수 있는 좋은 방법입니다.

## 매개변수의 순서

매개변수를 두 개 사용할 때 시작과 끝이라는 뉘앙스가 강하기 때문에 경계가 선명하여 가장 좋습니다.

```javascript
function someFunc(someArg1, someArg2) {}

genRandomNumber(1, 50);
getDates("2023-10-18", "2023-10-19");
genShuffleArray(1, 5);
```

만약 두 개 이상의 매개변수를 사용할 때는 객체를 사용하거나 rest parameter를 사용하거나 `arguments` 객체를 활용하거나 wrapping하는 방법이 있습니다.

```javascript
// 객체 사용

function someFunc({ someArg1, someArg2, someArg3, someArg4 }) {}
```

```javascript
// wrapping하는 방법

function someFunc(someArg1, someArg2, someArg3, someArg4) {}

function getFunc(someArg1, someArg3) {
  someFunc(someArg1, undefined, someArg3);
}
```

## 참고

[클린코드 자바스크립트(JavaScript)](https://www.udemy.com/course/clean-code-js/)
