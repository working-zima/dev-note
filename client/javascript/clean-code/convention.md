# 클린코드 for 자바스크립트(함께하기)

## 1. 공백

공백도 코드 작성의 일부입니다.
선언, 로직, 반환을 공백으로 구분지어 작성하면 가독성을 놓일 수 있습니다.

```javascript
const loadingElements = () => {
  // 1. 선언
  const el = document.createElement('div');
  const el2 = document.createElement('div');
  const el3 = document.createElement('div');

  // 2. 로직, 문
  el.setAttribute('class', 'loading-1')
  el2.setAttribute('class', 'loading-2')
  el3.setAttribute('class', 'loading-3')

  // 2. 로직, 문
  el.append(el2):
  el2.append(el3):

  // 2. 로직, 문
  if (el2) {
    // 3. 반환
    return el;
  }
}
```

### padding-line-between-statements

eslint에서 `padding-line-between-statements` 플래그를 사용하면 편하게 나눌 수 있습니다.

## 2. indent depth

depth가 많아지면 가독성이 좋지 않습니다.

```javascript
// depth가 길어지는 예시

if (true) {
  if (true) {
    if (true) {
      if (true) {
      }
    }
  }
}
```

### 해결

depth를 줄이기 위한 방법으로 아래와 같은 방법이 있습니다.

#### 의식적인 방법

1. 조기 반환 (early return)
2. callback 보다는 Promise를, Promise 보다는 Async & Await
3. 고차 함수 (map, reduce, filter ...)
4. 함수 나누고 추상화
5. 메서드 체이닝 (.then().then().then())

#### 도구를 사용하는 방법

1. editor config으로 indent_style과 indent_size
2. prettier의 tab width
3. eslint의 indent

## 3. 스타일 가이드

스타일 가이드는 네이밍 컨벤션을 포함하는 규칙을 위한 가이드라인으로 하나의 팀 혹은 집단을 위해 존재 즉 협업에 큰 도움을 주기 위하여 사용합니다.

### 대표적인 스타일 가이드

- [Vue 관련 코드에 대한 공식 스타일 가이드](https://ko.vuejs.org/style-guide/)

- [Redux Style Guide](https://redux.js.org/style-guide/)

- [Google Style Guides](https://google.github.io/styleguide/)

- [JavaScript Standard Style](https://standardjs.com/rules-kokr.html)

- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)

- [microsoft/rushstack](https://github.com/microsoft/rushstack)

- [MDN Guidelines for writing JavaScript code examples](https://developer.mozilla.org/en-US/docs/MDN/Writing_guidelines/Writing_style_guide/Code_style_guide/JavaScript)

이외에도 eslint custom을 통해 나만의 스타일 가이드를 사용하는 방법도 있습니다.

## 참고

[클린코드 자바스크립트(JavaScript)](https://www.udemy.com/course/clean-code-js/)
