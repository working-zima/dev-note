# Mock Function

## jest.fn()

Mock 함수 인스턴스를 만드는 가장 간단한 방법은 `jest.fn()` 을 쓰는 것입니다.

```javascript
const myMock = jest.fn();
```

Mock 함수에 인자를 넘겨 호출할 수 있습니다.

```javascript
myMock();
myMock(1);
myMock("a");
myMock([1, 2], { a: "b" });
```

## mock.calls

`jest.fn()`으로 만든 mock 함수에는 `mock` 프로퍼티가 있습니다.
`mock` 프로퍼티에는 `calls`라는 배열이 있습니다.

```javascript
// fn.test.js

const mockFn = jest.fn();

mockFn();
mockFn(1);
// mockFn.mock은 [[], [1]]

test("함수는 2번 호출됩니다.", () => {
  expect(mockFn.mock.length).toBe(2);
});

test("2번째로 호출된 함수에 전달된 첫번째 인수는 1 입니다.", () => {
  expect(mockFn.mock.calls[1][0]).toBe(2);
});
```

```javascript
// fn.test.js

const mockFn = jest.fn();

function forEachAdd1(arr) {
  arr.forEach((num) => {
    mockFn(num + 1);
    // mockFn.mock은 [[11], [21], [31]]
  });
}

forEachAdd1([10, 20, 30]);

test("함수 호출은 3번 됩니다", () => {
  expect(mockFn.mock.calls.length).toBe(3);
});

test("전달된 값은 11, 21, 31", () => {
  expect(mockFn.mock.calls[0][0]).toBe(11);
  expect(mockFn.mock.calls[1][0]).toBe(21);
  expect(mockFn.mock.calls[2][0]).toBe(31);
});
```

## mock.result

`mock.results`에는 `return`되는 값이 들어 있습니다.

```javascript
// fn.test.js

const mockFn = jest.fn((num) => num + 1);

mockFn(10);
mockFn(20);
mockFn(30);

test("함수 호출은 3번 됩니다", () => {
  console.log(mockFn.mock.results);
  // [{ type: 'return', value: 11},
  // { type: 'return', value: 21},
  // { type: 'return', value: 31}]
  expect(mockFn.mock.calls.length).toBe(3);
});

test("10에서 1 증가한 값이 반환된다", () => {
  expect(mockFn.mock.results[0].value).toBe(11);
});

test("20에서 1 증가한 값이 반환된다", () => {
  expect(mockFn.mock.results[0].value).toBe(21);
});

test("30에서 1 증가한 값이 반환된다", () => {
  expect(mockFn.mock.results[0].value).toBe(31);
});
```

## mockReturnValue

모의 함수는 테스트 중에 코드에 테스트 값을 주입할 수도 있습니다.
`mockReturnValue()`을 사용하면 `mock` 함수의 `return` 값을 바꿀 수 있습니다.

테스트 중간에 바꿀 때는 `mockReturnValueOnce()` 를 사용합니다.

```javascript
// fn.test.js

const mockFn = jest.fn((num) => num + 1);

mockFn
  .mockReturnValueOnce(10)
  .mockReturnValueOnce(20)
  .mockReturnValueOnce(30)
  .mockReturnValue(40);

mockFn();
mockFn();
mockFn();
mockFn();

test("함수 호출은 3번 됩니다", () => {
  console.log(mockFn.mock.results);
  // [{ type: 'return', value: 10},
  // { type: 'return', value: 20},
  // { type: 'return', value: 30},
  // { type: 'return', value: 40}]
  expect("dd").toBe("dd");
});
```

어떤 숫자가 `num`으로 넘어오던지 결과가 `true`와 `false`를 번갈아가며 반환되도록 정해 놓을 수도 있습니다.

```javascript
// fn.test.js

mockFn
  .mockReturnValueOnce(true)
  .mockReturnValueOnce(false)
  .mockReturnValueOnce(true)
  .mockReturnValueOnce(false)
  .mockReturnValue(true);

// [1, 3, 5]
const result = [1, 2, 3, 4, 5].filter((num) => mockFn(num));

test("홀수는 1,3,5", () => {
  expect(result).toStrickEqual([1, 3, 5]);
});
```

## mockResolvedValue

`mockResolvedValue()` 함수를 사용하면 비동기 함수를 만들 수 있습니다.

```javascript
// fn.test.js

// mockFn의 resutn은 resolve된 Promise
mockFn.mockResolvedValue({ name: "Mike" });

test("받아온 이름은 Mike", () => {
  mockFn().then((res) => {
    expect(res.name).toBe("Mike");
  });
});
```

## jest.mock()

`sendEmail`과 `sendSMS` 함수를 모킹하고 싶습니다.

```javascript
// messageService.js

export function sendEmail(email, message) {
  /* 이메일 보내는 코드 */
}

export function sendSMS(phone, message) {
  /* 문자를 보내는 코드 */
}
```

이때 `jest.fn()`에 바로 할당할 수 없습니다.
이유는 `import` 키워드로 불러오기 된 함수들은 기본적으로 `const` 변수이기 때문에 한 번 초기화되면 다른 값으로 변경이 불가능하기 때문입니다.

```javascript
// userService.test.js

import { sendEmail, sendSMS } from "./messageService";

sendEmail = jest.fn(); // "sendEmail" is read-only.
sendSMS = jest.fn(); // "sendSMS" is read-only.
```

해결법으로는 `messageService` 모듈의 모든 함수를 하나의 객체로 불러오면, 객체의 속성으로 목(mock) 함수를 할당하는 것입니다.

```javascript
// userService.test.js

import * as messageService from "./messageService";

messageService.sendEmail = jest.fn();
messageService.sendSMS = jest.fn();
```

위의 방법보다 더 나은 해결법이 있습니다.

`jest.mock()`은 자동으로 모듈을 모킹을 해주기 때문에 위와 같이 직접 일일이 모킹을 해줄 필요가 없습니다.

```javascript
// userService.test.js

import { sendEmail, sendSMS } from "./messageService";

jest.mock("./messageService");

beforeEach(() => {
  // jest.fn()없이 sendEmail, sendSMS 사용
  sendEmail.mockClear();
  sendSMS.mockClear();
});
```

`jest.mock()`은 첫 번째 인자로 넘어온 모듈 내의 모든 함수를 자동으로 mock 함수로 바꿔주어 작성한 코드의 `return`을 정해줄 수 있습니다.

```javascript
// fn.js

const fn = {
  createUser: (name) => {
    console.log("사용자가 생성되었습니다.");
    return {
      name,
    };
  },
};

module.exports = fn;
```

```javascript
// fn.test.js

const fn = require("./fn");

// fn함수를 mock 함수로 바꿈
jest.mock("./fn");

// mock 함수로 바뀐 fn함수의 return을 고정
fn.createUser.mockReturnValue({ name: "Mike" });

test("create user", () => {
  const user = fn.createUser("Mike");
  expect(user.name).toBe("Mike");
});
```

## mockFn.mockImplementation(fn)

`jest.fn`에서 구현하는 것과 같습니다.
차이점은 모의 개체(`mockFn`)가 호출될 때 구현도 실행합니다.

```javascript
const mockFn = jest.fn();

mockFn.mockImplementation((scalar) => 42 + scalar);

mockFn(0); // 42
mockFn(1); // 43
```

```javascript
const mockFn = jest.fn((scalar) => 42 + scalar);

mockFn(0); // 42
mockFn(1); // 43
```

## 기타

```javascript
const mockFn = jest.fn();

mockFn(10, 20);
mockFn();
mockFn(30, 40);

test("한번 이상 호출되면 통과", () => {
  expect(mockFn).toBeCalled();
});

test("인자의 숫자만큼 호출되면 통과", () => {
  expect(mockFn).toBeCalledTimes(3);
});

test("인자의 값을 전달 받은 함수가 있다면 통과", () => {
  expect(mockFn).toBeCalledWith(10, 20);
});

test("마지막으로 실행된 함수가 인자의 값을 전달 받았다면 통과", () => {
  expect(mockFn).laseCalledWith(30, 40);
});
```

## jest.spyOn()

어떤 객체에 속한 함수의 구현을 가짜로 대체하지 않고, 해당 함수의 호출 여부와 어떻게 호출되었는지만을 알아내야 할 때 `jest.spyOn()`을 사용합니다.

```javascript
jest.spyOn(object, "methodName");
```

```javascript
const calculator = {
  add: (a, b) => a + b,
};

//  calculator 객체의 add라는 함수에 스파이를 붙였습니다.
const spyFn = jest.spyOn(calculator, "add");

const result = calculator.add(2, 3);

// add 함수의 호출 횟수가 1이면 통과
expect(spyFn).toBeCalledTimes(1);
// add 함수 인자에 2, 3이 넘어가면 통과
expect(spyFn).toBeCalledWith(2, 3);
// add 함수의 반환이 5라면 통과
expect(result).toBe(5);
```

## 예시

```javascript
// app.js

const { printTitle } = require("./util");

const button = document.querySelector("button");

button.addEventListener("click", printTitle);

exports.printTitle = printTitle;
```

```javascript
// util.js

const { fetchData } = require("./http");

const loadTitle = () => {
  return fetchData().then((extractedData) => {
    const title = extractedData.title;
    const transformedTitle = title.toUpperCase();
    return transformedTitle;
  });
};

const printTitle = () => {
  loadTitle().then((title) => {
    console.log(title);
    return title;
  });
};

exports.printTetle = printTitle;
```

```javascript
// http.js

const axios = require("axios");

const fetchData = () => {
  return axios
    .get("https://jsonplaceholder.typicode.com/todos/1")
    .then((response) => {
      return response.data;
    });
};

exports.fetchData = fetchData;
```

`loadTitle` 함수는 API를 사용하고 있습니다.
http 요청을 사용하는 코드에서는 http 요청을 만드는 것이 좋습니다.
이유는 프론트엔드의 테스트 목적은 API가 올바르게 작동하는지가 아니기 때문입니다.

특히 axios에 의해 노출된 get 메서드의 작동 여부도 테스트하지 않는 게 좋습니다.
axios는 제3자 패키지에 의해 제공되며 우린 그 패키지의 역할에 의존합니다.
그러므로 제3자 패키지의 기능 역시 테스트하지 않습니다.

## http.js 대체

`__mocks` 폴더를 만들어 `http.js` 파일에 mock 코드를 작성합니다.

```javascript
// __mocks__/http.js

const fetchData = () => {
  return Promise.resolve({ title: "delectus aut autem" });
};

exports.fetchData = fetchData;
```

`jest.mock("./http");` 코드를 작성하여 `http.js` 파일을 대체합니다.

```javascript
// util.test.js

jest.mock("./http");

const { loadTitle } = require("./util");

test("should print an uppercase text", () => {
  loadTitle().then((title) => {
    expect(title).toBe("DELECTUS AUT AUTEM");
  });
});
```

## axios 대체

`__mocks` 폴더를 만들어 `axios.js` 파일에 mock 코드를 작성합니다.

```javascript
// axios.js

const get = (url) => {
  return Promise.resolve({ data: { title: "delectus aut autem" } });
};

exports.get = get;
```

axios는 `jest.mock()`을 사용하지 않아도 됩니다.
Jest는 `node_modules` 폴더에서 외부 모듈을 자동 모킹할 수 있습니다.

```javascript
const { loadTitle } = require("./util");

test("should print an uppercase text", () => {
  loadTitle().then((title) => {
    expect(title).toBe("DELECTUS AUT AUTEM");
  });
});
```
