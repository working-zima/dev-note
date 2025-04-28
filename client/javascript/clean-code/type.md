# 클린코드 for 자바스크립트(타입)

## 타입 검사

### typeof

typeof 피연산자를 평가하여 문자열로 반환합니다.
주로 원시형 데이터의 타입을 감별할 때 사용합니다.

```javascript
typeof "문자열"; // 'string'
typeof true; // 'boolean'
typeof undefined; // 'undefined'
typeof 123; // 'number'
typeof Symbol(); // 'symbol'
```

자바스크립트는 동적인 언어이기 때문에 타입도 동적으로 변할 수 있습니다.
이에 따라 typeof는 여러 문제가 있습니다.

#### function & class

함수의 타입도 감별할 수 있지만, 클래스도 'function'으로 판단되는 만큼 추천하지 않습니다.

```javascript
function myFunction() {}
class MyClass {}

typeof myFunction; // 'function'
typeof myClass; // 'function'
```

#### wrapper 객체

Number, String, Boolean, Symbol, BigInt 등의 wrapper 객체로 생성된 값도 'object'로 판단됩니다.

```javascript
const str = new String("문자열");

typeof str; // 'object'
```

#### null

null도 'object'로 판단되는 언어적 오류도 존재합니다.

```javascript
typeof null; // 'object'
```

### instanceof

객체의 프로토타입 체인을 검사합니다.
true, false를 반환합니다.

[프로토 타입](https://velog.io/@zimablue14/%ED%94%84%EB%A1%9C%ED%86%A0-%ED%83%80%EC%9E%85)에 관한 내용은 연결된 링크로 확인할 수 있습니다.
[instanceof](https://velog.io/@zimablue14/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EC%8A%A4-%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C-%EA%B0%9C%EB%B0%9C%EC%9D%84-%EC%9C%84%ED%95%9C-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B81)의 사용 예시는 연결된 링크로 확인할 수 있습니다.

```javascript
function Person(name, age) {
  this.name = name;
  this.name = age;
}

const blue = {
  name: "zima",
  age: 100,
};

const zima = new Person("zima", 100);

blue instanceof Person; // false
zima instanceof Person; // true
```

instanceof를 사용할 때도 취약점이 있습니다.
참조타입은 프로토타입 체인을 따라 올라가면 결국 Object이기 때문에 피연산자가 Object일 경우 true로 판단됩니다.

```javascript
const arr = [];
const func = function () {};
const date = new Date();

arr instanceof Array; // true
func instanceof Function; // true
date instanceof Date; // true

arr instanceof Object; // true
func instanceof Object; // true
date instanceof Object; // true
```

#### isArray

배열을 판별할 때는 `Array.isArray()`를 사용합니다.
`Array.isArray()` 메서드는 인자가 Array인지 판별합니다.

```javascript
Array.isArray(obj);
```

### Object.prototype.toString.call()

instanceof와 비슷하지만 프로토타입 체인을 역 이용하는 방법도 있습니다.
객체에 `toString()`을 사용하는 것입니다.

`toString()`에 관한 내용은 [객체의 형변환](https://velog.io/@zimablue14/%EA%B0%9D%EC%B2%B4%EC%9D%98-%ED%98%95-%EB%B3%80%ED%99%98)을 참고하세요

`toString()`으로 일반 객체를 문자열로 변화하면 ``"[object Type]"` 형태가 됩니다.

```javascript
let objectToString = Object.prototype.toString();

console.log(objectToString); // [object Object]
```

```javascript
// 객체에서 내장 toString을 추출하는 게 가능합니다.
let objectToString = Object.prototype.toString;

let arr = [];

// call을 사용해 컨텍스트를 this=arr로 설정하고 함수 objectToString를 실행
// toString 알고리즘은 내부적으로 this를 검사하고 상응하는 결과를 반환
alert(objectToString.call(arr)); // [object Array]
```

이렇게 타입 검사를 사용할 수 있습니다.
동작 대상은 원시형, 내장 객체, Symbol.toStringTag가 있는 객체이고 문자열을 반환합니다.

```javascript
const arr = [];
const func = function () {};
const date = new Date();

Object.prototype.toString.call(arr); // '[object Array]'
Object.prototype.toString.call(func); // '[object Function]'
Object.prototype.toString.call(date); // '[object Date]'
```

## undefined & null

`undefined`는 정의되지 않은것 이고, `null`은 없는 것을 명시적으로 표현한 것입니다.

### null

typeof는 `null`을 'object' 취급합니다.

```javascript
let varb = null;

typeof varb; // 'object'
```

`null`은 수학 연산에서 0으로 취급합니다.

```javascript
null + 1234; // 123
```

### undefined

변수를 선언하고 값을 할당하지 않았을 때 변수는 `undefined`를 갖습니다.

```javascript
let varb;

typeof varb; // 'undefined'
```

`undefined`는 숫자 취급되지 않습니다.

```javascript
undefined + 1234; // NaN
```

이처럼 `null`과 `undefined`는 비슷하지만 전혀 다른 결과를 가져오기 때문에 비어있는 값을 둘 중 하나로 통일하는 컨벤션을 만드는 것이 좋습니다.

## eqeq 줄이기

### equality

equality(`==`)를 사용하면변수의 type을 강제로 다른 type으로 변경하는 type casting이 발생합니다.

```javascript
"1" == 1; // true
1 == true; // true
```

### Strict equality

Strict equality(`===`)를 사용하면 type까지 동등한지 확인합니다.

```javascript
"1" === 1; // false
1 === true; // false
1 === 1; // true
```

### equality의 유혹을 받는 예시

비교하려는 숫자가 문자형일 때 equality 연산자를 통해 해결하려고 할 수 있습니다.

```html
<label for="ticketNum">Number of tickets you would like to buy:</label>
<input id="ticketNum" type="number" name="ticketNum" value="0" />
```

```javascript
const ticketNum = $("ticketNum");

ticketNum.value; // "0"

ticketNum.value === 0; // false
ticketNum.value == 0; // true
```

하지만 equality는 문제를 이르킬 가능성이 높기 때문에 사용을 지양해야합니다.
따라서 형 변환을 수동으로 하여 비교하는 것을 추천합니다.

```javascript
const ticketNum = $("ticketNum");

Number(ticketNum.value) === 0; // true
ticketNum.valueAsNumber === 0; // true
```

## 형 변환 주의하기

사용자가 아닌 자바스크립트가 형 변환을 일으키는 것을 암묵적인 형 변환이라고 합니다.
자바스크립트에는 암묵적인 형 변환이 많이 일어납니다.
암묵적인 형 변환은 예측하기 힘들기 때문에 사용자가 직접 명시적인 형 변환하는 것을 지향해야 합니다.

### 문자와 결합

-> 문자

```javascript
11 + "문자"; // "11문자"
```

### 논리 NOT 연산

-> 불리언

```javascript
!!"문자"; // true
!!""; // false
```

### 덧셈 연산자

-> 숫자

```javascript
+"9.99"; // 9
```

### 웬만하면 명시적으로 전환하는 것이 좋다

-> 문자

```javascript
String(11 + "문자"); // "11문자"
```

-> 불리언

```javascript
Boolean("문자"); // true
Boolean(""); // false
```

-> 숫자

```javascript
Number("9.99"); // 9
```

```javascript
parseInt("9.99", 10); // 9
```

## isNaN() (is Not A Number)

`isNan`은 숫자가 아닐 때 `true`를 반환합니다.
그래서 typeof와 혼합해서 사용하다 보면 실수가 나올 수 있습니다.

```javascript
typeof 1234 === "number"; // true
isNaN(123); // false
```

안그래도 실수가 나올 요소가 있는데 값은 먼저 숫자로 형 변환 후,
`NaN`인지 판별하기 때문에 예상치 못한 결과를 내기까지 합니다.

```javascript
isNaN(NaN); // true
isNaN(undefined); // true, Number(undefined)가 NaN을 반환하기 때문
isNaN({}); // true, Number({})가 NaN을 반환하기 때문
isNaN(0 / 0); // true
isNaN("123ABC"); // true: parseInt("123ABC")는 123이지만 Number("123ABC")는 NaN을 반환
isNaN(123 + "테스트"); // true

isNaN(true); // false
isNaN(null); // false
isNaN(37); // false

isNaN(""); // false: Number("")는 NaN이 아닌 0을 반환
isNaN(" "); // false: Number(" ")는 NaN이 아닌 0을 반환
isNaN("abc"); // true: Number("asd")는 NaN 반환
```

### Number.isNaN()

`Number.isNaN()`은 엄격한 버전의 `isNaN()`입니다.
값을 숫자로 변환하지 않고, 주어진 값의 유형이 `Number`이고 값이 `NaN`이면 `true`, 아니면 `false`를 반환합니다.
즉, 숫자가 맞는지 아닌지가 아닌 `NaN`값이 맞는지 아닌지만 판별합니다.
따라서 만약 `isNaN()`을 사용하려는 경우에는 `iNumber.isNaN()`을 사용하는 것이 실수가 적을 수 있습니다.

```javascript
Number.isNaN(NaN); // true
Number.isNaN(Number.NaN); // true
Number.isNaN(0 / 0); // true

Number.isNaN(true); // false
Number.isNaN(null); // false
Number.isNaN(37); // false
Number.isNaN("37"); // false
Number.isNaN("37.37"); // false
Number.isNaN(""); // false
Number.isNaN(" "); // false

// isNaN()으로는 true
Number.isNaN("NaN"); // false
Number.isNaN(undefined); // false
Number.isNaN({}); // false
Number.isNaN("abc"); // false
Number.isNaN(123 + "테스트"); // false
```

## 참고

[클린코드 자바스크립트(JavaScript)](https://www.udemy.com/course/clean-code-js/)
[1ystar](https://on1ystar.github.io/javascript/2021/03/30/JavaScript-7/)
