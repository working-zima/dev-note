# This

자신이 속한 객체를 가리키는 변수입니다.

`this` 는 함수가 호출되고 실행 컨텍스트가 생성되면 binding 됩니다.\
즉, `this` 는 함수가 호출될 때 결정되며 호출하는 방식에 따른 차이가 있습니다.

호출하는 방식은 아래와 같습니다.

## 전역공간

전역공간에서의 `this`는 전역 객체를 가리킵니다.

전역 객체는 host 객체라고 하며 자바스크립트가 실행되는 환경인 런타임에 따라서 전역객체의 정보가 달라집니다.

전역 공간에서 `console.log(this)`를 실행하면 호스트 가 만든 `this` 정보를 제공하며 브라우저에서는 window, node.js에서는 global이 됩니다.

## 함수 호출

일반적인 함수 내부에서 `this`는 전역공간과 마찬가지로 전역 객체를 가리킵니다.\
함수 내부에서 console.log(`this`)를 실행하면 브라우저는 window, node.js는 global이 출력됩니다.

arrow 함수에서 `this`는 바로 위 컨텍스트를 가리킵니다.

## 메서드 호출

메서드는 원래 함수이지만 어떤 객체와 관련된 동작을 하게 되면 “메서드” 로서 호출합니다.\
메서드에서 `this`는 호출 주체(메서드명 앞)를 카리킵니다.

즉, "`.`"과 "`[ ]`" 앞에 있는 것을 가리킵니다.

```javascript
let a = {
  b: function() {
      console.log(this) // {b: f}
    }
}

a.b(); or a[b](); // a가 this
```

## callback 호출

기본적으로는 함수내부에서와 동일합니다.\
경우에 따라서 제어권을 가진 함수가 콜백의 `this`를 지정해둔 경우가 있으며 개발자가 원하는 경우 `this` 바인딩 메소드를 사용하여 바꿀 수 있습니다.

### 명시적으로 this를 바인딩 하는 세 가지 방법

아래의 메소드들은 주어진 `this` 값 및 각각 전달된 인수와 함께 함수를 호출합니다.\
`this` 매개변수는 필수, 이후 매개변수는 생략 가능합니다.

#### call 메서드

```jsx
함수.call( this, 함수의 매개변수1, 함수의 매개변수2, … );
```

#### apply 메서드

```jsx
함수.apply( this, [ 함수의 매개변수1, 함수의 매개변수2, …] );
```

#### bind 메서드

```jsx
var 변수 = 함수.bind( this );
변수( 함수의 매개변수1, 함수의 매개변수2, ...)
```

```jsx
var 변수 = 함수.bind( this, 함수의 매개변수1, 함수의 매개변수2 );
변수( 함수의 매개변수3 )
```

#### 예시

```javascript
function a(x, y, z) {
  console.log(this, x, y, z);
  // {bb: "bbb"} 1 2 3
}

let b = {
  bb: "bbb",
};

a.call(b, 1, 2, 3);

a.apply(b, [1, 2, 3]);

let c = a.bind(b);
c(1, 2, 3);

let d = a.bind(b, 1, 2);
d(3);
```

### 명시적이지 않은 callback 함수 예시

#### 기본적인 경우

```jsx
var callback = function () {
  console.dir(this);
  // 함수 callback은 cb() 라는 함수로서 호출되었기 때문에
  // this는 window/ gloal 출력
};
var obj = {
  a: 1,
  b: function (cb) {
    cb();
  },
};
obj.b(callback);
// 변수 obj 안의 함수 b의 매개변수에 callback을 전달
// 함수 b는 함수 callback을 호출
```

#### 위의 예시에 this 바인딩 메서드 사용할 경우

```jsx
var callback = function () {
  console.dir(this);
  // 함수 callback은 cb() 라는 함수로서 호출되어서 this는 window/ gloal 이지만
  // call메서드에 의해 this를 받았기 때문에 obj가 출력
};
var obj = {
  a: 1,
  b: function (cb) {
    cb.call(this);
    // b컨텍스트 안에서의 this는 객체 obj
  },
};
obj.b(callback);
// 변수 obj 안의 함수 b의 매개변수에 callback을 전달
// 함수 b는 함수 callback을 호출
```

#### 제어권을 가진 함수가 콜백의 this를 지정하지 않은 경우

```jsx
var callback = function () {
  console.dir(this);
  // 기본적인 callback 호출에서 this 는 전역객체 window/ global
};
var obj = {
  a: 1,
};
setTimeout(callback, 100);
```

```jsx
var callback = function () {
  console.dir(this);
  // 기본적인 callback 호출에서 bind()메서드를 사용하였기 때문에
  // this 는 obj
};
var obj = {
  a: 1,
};
setTimeout(callback.bind(obj), 100);
```

#### 제어권을 가진 함수가 콜백의 this를 지정한 경우

`addEventListerner` 라는 함수의 경우에 콜백함수를 처리할 때 `this`는 이벤트가 발생한 그 타겟 대상 엘리먼트로 하도록 정의되어 있습니다.\
따라서 `this`는 타겟 html dom 엘리먼트가 됩니다.

```jsx
document.body.innerHTML += '<div id="a">클릭하세요</div>';

document.getElementById("a").addEventListener("click", function () {
  console.dir(this);
  //  div#a
});
```

`addEventListerner` 라는 함수는 콜백함수를 처리할 때 `this`는 이벤트가 발생한 그 타겟 대상 엘리먼트로 하도록 정의되어 있습니다.\
그래서 `this`는 타겟 html dom 엘리먼트가 되는데 bind() 메서드로 `this`를 `obj` 설정하면 `this`는 `obj`가 됩니다.

```jsx
document.body.innerHTML += '<div id="a">클릭하세요</div>';
var obj = { a: 1 };

document.getElementById("a").addEventListener(
  "click",
  function () {
    console.dir(this);
    // Object
  }.bind(obj)
);
```

### 생성자함수 호출

생성자 함수를 바탕으로 `new` 연산자로 새로 만들어진 인스턴스 객체가 `this`가 됩니다.

#### new 연산자가 없는 경우

```javascript
function Person(n, a) {
  this.name = n;
  this.age = a;
  // 함수에서 선언되었으므로 this는 전역객체를 가리킴
}

let ruel = Person("성율", 32);

console.log(window.name, window.age);
// ruel 32
```

#### new 연산자가 있는 경우

```javascript
function Person(n, a) {
  this.name = n;
  this.age = a;
  // this는 새로 만들어진 인스턴스인 ruel을 가리킴
}

let ruel = new Person("성율", 32);

console.log(ruel);
// {name: ruel, age: 32}
```

## 요약

### this란?

자신이 속한 객체를 가리키는 변수입니다.

### this는 언제 결정이 되나요?

함수가 호출되고 실행 컨텍스트가 생성되면 `this`가 binding이 됩니다.

### this는 어떻게 결정이 되나요?

`this`는 호출하는 방식에 따라 자신이 속해지는 객체가 달라집니다.\
방식에는 전역공간, 함수, 메서드, 콜백, 생성자 함수가 있습니다.

1. 전역공간에서의 `this`는 전역 객체를 가리킵니다.\
   브라우저에서는 window, nodejs에서는 global을 가리킵니다.

2. 일반적인 함수 내부에서 `this`는 전역공간과 마찬가지로 전역 객체를 가리킵니다.\
   다만 arrow 함수에서 `this`는 현재 컨텍스트 바로 위 컨텍스트를 가리킵니다.

3. 메서드에서는 호출 주체(메서드명 앞)를 카리킵니다.

4. callback의 경우에는 기본적으로 함수 내부와 동일하나 경우에 따라서 제어권을 가진 함수가 콜백의 `this`를 지정해둔 경우가 있으며 개발자가 원하는 경우 `this` 바인딩 메소드를 사용하여 바꿀 수 있습니다.

5. 생성자 함수는 새로 만들어진 인스턴스 객체가 `this`가 됩니다.
