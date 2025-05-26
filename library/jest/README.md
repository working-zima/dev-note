---
description: 페이스북에서 만들어 React와 더불어 많은 자바스크립트 개발자들로부터 좋은 반응을 얻고 있는 테스트 프레임워크 겸 테스트 러너인 Jest에 관해 정리한 내용입니다.
---

# Jest

프로젝트의 개발 의존성이고 테스트를 실행하고 결과를 확인하거나 조건 혹은 어서션(Assertion)을 작성하는 도구입니다.

## 테스트 러너 (Test Runner)

테스트 코드를 찾아서 실행하고, 통과/실패 결과를 출력해주는 도구입니다.\
테스트 파일 탐색, 실행 흐름 제어, 결과 리포팅, watch 모드, 커버리지 출력 등을 합니다.

## 테스트 프레임워크 (Test Framework)

테스트 구조를 정의하고, 테스트 수행 단위와 어설션(검증) 방식 등을 제공합니다.\
`describe()`, `test()` 같은 테스트 구조, 테스트 훅(`beforeEach`, `afterAll`) 제공, 어설션 문법 정의합니다.

## 설치

```bash
npm install --save-dev jest
```

## 실행

"jest"로 실행하면 자동으로 .test나 .spec으로 끝나는 이름의 파일을 실행합니다

```bash
jest
```

## 감시

"jest --watch"로 실행하면 Jest는 감시를 시작합니다.
감시가 시작되면 모든 테스트를 재실행하거나 다른 작업도 가능합니다.
(파일을 변경하고 저장하면 Jest가 자동으로 테스트를 재실행)

또한 바로가기(Watch Usage)를 사용하여 감시자를 제어할 수도 있습니다.

```bash
jest --watch
```

`jest --watch` 는 Watch Usage의 `o`로 테스트를 실행하게 됩니다.
`o`는 hg나 git에서 변경된 파일만 실행하는 옵션이기 때문에 이미 커밋된 테스트에 대해서는 실행되지 않습니다.

이럴 땐 `jest --watchAll`을 사용합니다.

```bash
jest --watchAll
```

자세한 내용은 [Jest 공식 문서](https://jestjs.io/docs/getting-started)에서 확인할 수 있습니다.

## 단언(Assertion)

테스트 성공과 실패의 원인이 되는 부분을 말합니다.
Jest에서 제공하며 Assertion Library에 해당합니다.

### 단언의 형태

```jsx
expect(linkElement).toBeInTheDocument();
```

- expect: 단언을 시작하는데 사용

- expect argument: 단언의 대상

- matcher: 단언의 타입(Jest DOM에서 가져옴)

단언은 Jest의 전역 메서드인 `expect`로 시작합니다.
`expect` 다음은 `Matcher`가 오게 됩니다.

`Matcher`는 단언의 유형으로 여러가지를 사용할 수 있습니다.
위의 코드는 Jest DOM의 `toBeInTheDocument`를 사용했으며 `toBeInTheDocument`는 해당 element가 document으며 에 존재하는지 확인할 때 사용합니다.

`toBeInTheDocument`는 가상 DOM에만 사용할 수 있으며 모든 노드에 적용할 수 있는 `toBe`와 `toHaveLength` 같은 매처도 있습니다.

## Matcher

### Common Matchers

- `toBe(값)`
  : 원시형 값이 정확히 일치하는지 테스트 할 때 사용합니다.

- `toEqual(값)`
  : 참조형 값이 정확히 일치하는지 테스트 할 때 사용합니다.

- `toStrictEqual(값)`
  : `toEqual`과 비슷하지만 특정 요소에 undefined가 나오는 것을 허용하지 않습니다.

### Truthiness

- `toBeNull()`
  : `null`에만 일치합니다.

- `toBeUndefined()`
  : `undefined`에만 일치합니다.

- `toBeDefined()`
  : `toBeUndefined`의 반대입니다.

- `toBeTruthy()`
  : `if` 구문의 `condition`이 `true`로 취급하는 모든 것과 일치합니다.

- `toBeFalsy()`
  : `if` 구문의 `condition`이 `false`로 취급하는 모든 것과 일치합니다.

`if` 구문의 `condition`이란 아래와 같습니다.

```javascript
if (condition)
  statement1
  [else
   statement2]
```

### Numbers

- `toBeGreaterThan(숫자)`
  : 인자로 들어온 숫자보다 커야합니다. (초과)

- `toBeGreaterThanOrEqual(숫자)`
  : 인자로 들어온 숫자보다 크거나 같아야 합니다. (이상)

- `toBeLessThan(숫자)`
  : 인자로 들어온 숫자보다 작아야 합니다. (미만)

- `toBeLessThanOrEqual(숫자)`
  : 인자로 들어온 숫자보다 작거나 같아야 합니다. (이하)

- `toBeCloseTo(숫자)`
  : 부동 소수점 숫자가 정확히 일치하는지 테스트 할 때 사용합니다.
  부동 소수점 숫자를 `toBe()` 또는 `toEqual()`로 테스트할 경우 반올림 오류로 동작하지 않을 수 있습니다.
  > 자바스크립트에서 0.1 + 0.2는 0.3이 아니고 0.30000000000000004으로 계산됩니다.
  > 컴퓨터는 숫자를 계산할 때 2진법으로 계산합니다.
  > 몇몇 소수는 10진법에서 2진법으로 변환하는 과정에서 무한 소수가 되어버립니다.
  > 이때 저장공간의 한계가 있는 컴퓨터는 무한 소수를 유한 소수로 바꾸게 됩니다.
  > 이 과정에서 미세한 오차가 발생하면서 값들이 손실되거나 초과하게 됩니다.
  > 출처: [harimad.log](https://velog.io/@harimad/0.10.2-0.3-in-JS)

### Strings

- `toMatch(정규식)`
  : 정규식과 비교하여 문자열을 검사할 수 있습니다.

- `stringContaining(문자열)`
  : 주어지는 문자를 포함하고 있는지 테스트합니다.

### Arrays and iterables

- `toContain(특정 항목)`
  : 배열이나 이터러블이 특정 항목을 포함하는지 여부를 확인 할 수 있습니다.

### Exceptions

- `toThrow(오류 메세지 or 정규식)`
  : 특정 함수가 호출될 때 오류를 발생시키는지를 테스트합니다.
  예외를 던지는 함수는 래핑 함수 내에서 호출해야 합니다.
  그렇지 않으면 `toThrow` assertion이 실패합니다.

## 유닛 테스트 예시

아래의 함수는 의존성이 없이 단순히 입력과 인자 두 개를 갖고 출력을 반환하고 있습니다.

```javascript
// util.js

exports.generateText = (name, age) => {
  return `${name} (${age} years old)`;
};
```

### .test, .spec

Jest는 자동으로 `.test`와 `.spec`이 포함된 파일을 탐지하여 실행하기 때문에 `.test`를 파일명에 사용하여 테스트를 작성할 파일을 만들어 줍니다.

새로 만든 파일에 테스트할 함수를 가져옵니다.

```javascript
// util.test.js

const { generateText } = require("./util");
```

### test()

`test()` 함수는 Jest에서 제공하며 Test Runner에 해당합니다.
테스트를 실행할 때 사용하며 첫 번째 인자로는 텍스트로된 설명을 사용합니다.
두 번째 인자로는 테스트를 실행하기 위해 Jest가 실행할 익명함수를 전달합니다.

```javascript
// util.test.js

const { generateText } = require("./util");

test("should output name and age", () => {
  const text = generateText("Max", 29);
});
```

### expect()

비교하고 싶은 내용을 전달하고 `expect(text)`가 특정한 텍스트와 동일하다면 여러 헬퍼 함수를 사용합니다.

```javascript
// util.test.js

const { generateText } = require("./util");

test("should output name and age", () => {
  const text = generateText("Max", 29);
  expect(text).toBe("Max (29 years old)");
});
```

### 성공한 테스트 결과

<image src="https://velog.velcdn.com/images/zimablue14/post/a454d076-b6b2-4469-beb2-ef84e15988d0/image.png"/>

#### 실패한 테스트 결과

테스트할 함수를 변경하여 실패로 만들어 보겠습니다.

```javascript
// util.js

exports.generateText = (name, age) => {
  // Returns output text
  return `${age} (${age} years old)`;
};
```

`Expected`는 예상한 내용, `Received`는 실제 얻은 내용입니다.

<image src="https://velog.velcdn.com/images/zimablue14/post/5bf57629-e925-4e9a-adb0-87e393f996a9/image.png"/>

### 테스트 추가

만약 테스트할 함수의 반환값을 아래의 값으로 바꾼다면 실제로는 옳지 않은 함수이지만 테스트 함수를 통과하는 거짓 긍정을 피할 수 없습니다.

```javascript
// util.js

exports.generateText = (name, age) => {
  return "Max (29 years old)";
};
```

```javascript
// util.test.js

const { generateText } = require("./util");

test("should output name and age", () => {
  const text = generateText("Max", 29);
  expect(text).toBe("Max (29 years old)");
});
```

이 경우 두 번째 테스트를 작성하여 반대 상황을 확인하거나 같은 내용을 다른 인자로 이중 확인할 수 있습니다.

```javascript
// util.test.js

const { generateText } = require("./util");

test("should output name and age", () => {
  const text = generateText("Max", 29);
  expect(text).toBe("Max (29 years old)");
  const text2 = generateText("Anna", 28);
  expect(text2).toBe("Anna (28 years old)");
});

test("should output data-less text", () => {
  const text = generateText();
  expect(text).toBe("undefined (undefined years old)");
});
```

### 통합 테스트 예시

아래의 함수들은 입력이 없고 출력도 반환하지 않습니다.
대신 다른 함수를 많이 사용하여 의존성이 높으며, DOM을 추가한 함수입니다.

```javascript
// app.js

const { checkAndGenerate, createElement } = require("./util");

const initApp = () => {
  // app을 초기화하고 button click listener를 등록
  const newUserButton = document.querySelector("#btnAddUser");
  newUserButton.addEventListener("click", addUser);
};

const addUser = () => {
  // 사용자 입력을 가져오고 이를 기반으로 새로운 HTML 요소를 만들고
  // 요소를 DOM에 추가
  const newUserNameInput = document.querySelector("input#name");
  const newUserAgeInput = document.querySelector("input#age");

  const outputText = checkAndGenerate(
    newUserNameInput.value,
    newUserAgeInput.value
  );

  if (!outputText) return;

  const userList = document.querySelector(".user-list");

  const element = createElement("li", outputText, "user-item");
  userList.appendChild(element);
};

// 앱 시작
initApp();
```

`addUser` 함수는 요소 몇 가지를 선택해서 입력을 검사하고 해당 입력을 기반으로 텍스트를 생성합니다.

```javascript
// app.js

const addUser = () => {
  const newUserNameInput = document.querySelector("input#name");
  const newUserAgeInput = document.querySelector("input#age");

  // Input 입력 값 유효성 검사하고 어떤 텍스트 생성
  const outputText = checkAndGenerate(
    newUserNameInput.value,
    newUserAgeInput.value
  );

  if (!outputText) return;

  const userList = document.querySelector(".user-list");
  // 어떤 텍스트를 사용하여 새로운 HTML 요소를 만듦
  const element = createElement("li", outputText, "user-item");
  // 새로운 요소를 DOM에 추가
  userList.appendChild(element);
};
```

### checkAndGenerate 함수

통합 테스트는 실제로 테스트되는 유닛에 의존하기 때문에 통합 테스트를 작성하기 전 가능한 한 제일 작은 레벨까지 자세하게 살펴봅니다.

즉, generateText와 validateInput의 유닛 테스트를 작성하고 잘 작동하는지 확인하는 것이 중요합니다.

```javascript
// util.js

const generateText = (name, age) => {
  // Returns output text
  return `${name} (${age} years old)`;
  // return "Max (29 years old)";
};

const validateInput = (text, notEmpty, isNumber) => {
  // Validate user input with two pre-defined rules
  if (!text) {
    return false;
  }
  if (notEmpty && text.trim().length === 0) {
    return false;
  }
  if (isNumber && +text === NaN) {
    return false;
  }
  return true;
};

exports.checkAndGenerate = (name, age) => {
  if (!validateInput(name, true, false) || !validateInput(age, false, true)) {
    return false;
  }
  return this.generateText(name, age);
};

exports.generateText = generateText;
exports.validateInput = validateInput;
```

#### checkAndGenerate 테스트 코드 작성

```javascript
const { generateText, checkAndGenerate } = require("./util");

test("should output name and age", () => {
  const text = generateText("Max", 29);
  expect(text).toBe("Max (29 years old)");
});

test("should generate a valid text output", () => {
  const text = checkAndGenerate("Max", 29);
  expect(text).toBe("Max (29 years old)");
});
```

### 성공한 테스트 결과

generateText 유닛 테스트와 checkAndGenerate 통합 테스트 모두 통과하였을 경우입니다.

<image src='https://velog.velcdn.com/images/zimablue14/post/22ab1cb3-4a1f-4776-9eab-d0e06775e0b9/image.png
'/>

#### 실패한 테스트 결과

만약 유닛 테스트한 코드는 옳바르나 통합 테스트할 코드가 잘못 작성되어 있을 경우 아래와 같은 결과를 갖게 됩니다.

```javascript
// util.js

exports.checkAndGenerate = (name, age) => {
  // 코드 실수
  if (validateInput(name, true, false) || !validateInput(age, false, true)) {
    return false;
  }
  return this.generateText(name, age);
};
```

<image src='https://velog.velcdn.com/images/zimablue14/post/f389921e-a63f-47de-9e92-d91d62d3e16f/image.png'/>
