# 2. React Testing Library

## 학습 키워드

- React Testing Library
- given - when - then 패턴
- Mocking
- Test fixture

## React Testing Library

💡 React 컴포넌트를 사용자 입장에 가깝게 테스트할 수 있는 도구입니다.

### 예시1

```tsx
// 기존 TextField.tsx

import React, { useRef } from 'react';

type TextFieldProps = {
  placeholder: string;
  filterText: string;
  setFilterText: (value: string) => void;
};

function TextField({ placeholder, filterText, setFilterText }: TextFieldProps) {
  const id = useRef(`input-${Math.random()}`);

  const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    const { value } = event.target;
    setFilterText(value);
  };

  return (
    <div>
      <label htmlFor={id.current}>
        Search
      </label>
      <input
        id={id.current}
        type="text"
        placeholder={placeholder}
        value={filterText}
        onChange={handleChange}
      />
    </div>
  );
}

export default TextField;
```

```jsx
// TextField.test.tsx

import { render, screen } from '@testing-library/react';

import TextField from './TextField';

test('TextField', () => {
  // given
  const text = 'Tester';
  const setText = () => {
    // do nothing...
  };

  // when
  render(
    <TextField
      label="Name"
      placeholder="Input your name"
      text={text}
      setText={setText}
    />,
  );

  // then
  screen.getByLabelText('Name');
});
```

```tsx
import React, { useRef } from 'react';

type TextFieldProps = {
  label: string
  placeholder: string;
  text: string;
  setText: (value: string) => void;
};

// label 추가
function TextField({ label, placeholder, text, setText }: TextFieldProps) {
  const id = useRef(`input-${Math.random()}`);

  const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    const { value } = event.target;
    // setFilterText -> setText
    setText(value);
  };

  return (
    <div>
      <label htmlFor={id.current}>
        {label}
      </label>
      <input
        id={id.current}
        type="text"
        placeholder={placeholder}
        value={text}
        onChange={handleChange}
      />
    </div>
  );
}

export default TextField;
```

테스트 코드, 즉 컴포넌트를 사용하는 코드를 작성하면서 해당 컴포넌트(`TextField`)의 인터페이스를 점검할 수 있습니다.\
기존에는 `label`이 빠져있었고, `text` 같이 범용적인 표현을 사용하지 않은 문제가 있었습니다.\
개발하면서 이런 문제를 발견할 수도 있지만, 우리가 테스트부터 작성했거나 빠르게 테스트 코드를 작성했다면 작성하기 전 또는 바로 직후에 문제를 발견해서 수정할 수 있었을 것입니다.\
시간이 지나면 해당 코드에 대한 지식이 감소하고, 자신감 또한 감소하기 때문에 건드리기 힘든 코드가 되기 십상입니다.

### Mocking

mocking은 단위 테스트를 작성할 때, 해당 코드가 의존하는 부분을 직접 생성하기가 너무 부담스러운 경우 가짜(mock)로 대체하는 기법을 말합니다.\
mocking을 사용하면 특정 기능만 분리해서 테스트하겠다는 단위 테스트(Unit Test)의 본연의 의도에 맞기 때문에 자주 사용됩니다.

#### jest.fn()

Jest는 가짜 함수(mock function)를 생성할 수 있도록 `jest.fn()` 함수를 제공합니다.\
반 자바스크립트 함수와 동일한 방식으로 인자를 넘겨 호출할 수 있습니다.

```jsx
const mock = jest.fn();

mock();
mock(1);
mock("a");
mock([1, 2], { a: "b" });
```

`mockReturnValue(리턴 값)` 함수를 이용해서 가짜 함수가 어떤 값을 리턴해야할지 설정해줄 수 있습니다.

```jsx
const mock = jest.fn();

mock.mockReturnValue(42);
mock(); // 42

mock.mockReturnValue(43);
mock(); // 43
```

`mockResolvedValue(Promise가 resolve하는 값)` 함수를 이용하면 가짜 비동기 함수를 만들 수 있습니다.

```jsx
const mock = jest.fn();

mock.mockResolvedValue("I will be a mock!");
mockFn().then((result) => {
  console.log(result); // I will be a mock!
});
```

`mockImplementation(구현 코드)` 함수를 이용하면 아예 해당 함수를 통째로 즉석해서 재구현해버릴 수도 있습니다.

```jsx
const mockFn = jest.fn(scalar => 42 + scalar);

mock(0); // 42
mock(1); // 43

mock.mockImplementation(scalar => 36 + scalar);

mock(2); // 38
mock(3); // 39
```

`toHaveBeenCalled` 함수를 사용하면 가짜 함수가 몇번 호출되었고 인자로 무엇이 넘어왔었는지를 검증할 수 있습니다.

```jsx
const mock = jest.fn();

mock("a");
mock(["b", "c"]);

expect(mock).toHaveBeenCalledTimes(2);
expect(mock).toHaveBeenCalledWith("a");
expect(mock).toHaveBeenCalledWith(["b", "c"]);
```

### jest.spyOn()

어떤 객체에 속한 함수의 구현을 가짜로 대체하지 않고, 해당 함수의 호출 여부와 어떻게 호출되었는지만을 알아내야 할 때 사용합니다.

`jest.spyOn()` 함수를 이용해서 `calculator` 객체의 `add`라는 함수에 스파이를 붙였습니다.\
add 함수를 호출 후에 호출 횟수와 어떤 인자가 넘어 갔는지 검증할 수 있습니다.

```jsx
jest.spyOn(object, methodName)
```

```jsx
const calculator = {
  add: (a, b) => a + b,
};

const spyFn = jest.spyOn(calculator, "add");

const result = calculator.add(2, 3);

expect(spyFn).toHaveBeenCalledTimes(1);
expect(spyFn).toHaveBeenCalledWith(2, 3);
expect(result).toBe(5);
```

#### fireEvent.change(element, options)

```jsx
fireEvent.change(element, options);
```

- `element`: 변경 이벤트를 발생시킬 DOM 엘리먼트입니다. 이 엘리먼트는 테스트에서 찾아지거나 선택되어야 합니다.

- `options`: 변경 이벤트에 대한 옵션 객체로, 다음과 같은 구조를 가집니다:
  - `target`: 변경된 값과 함께 전달될 이벤트 객체입니다. 주로 `value` 속성을 포함하며, 사용자가 폼에 입력한 값과 관련이 있습니다.

```jsx
fireEvent.change(getByLabelText(/username/i), {target: {value: 'a'}})
```

### 예시2

DCI 스타일로 코드를 바꾸고, 입력 등이 잘 작동하는지 확인해 봅시다.

```jsx
import { render, screen, fireEvent } from '@testing-library/react';

import TextField from './TextField';

const context = describe;

describe('TextField', () => {
// given
  const text = 'Tester';

  // setText mock 함수
  const setText = jest.fn();

  // setText 초기화하여 기록하고 있던 것들을 지움
  beforeEach(() => {
    setText.mockClear();
    // 또는 jest.clearAllMocks();
  });

  function renderTextField() {
    render((
      <TextField
      label="Name"
      placeholder="Input your name"
      text={text}
      setText={setText}
      />
    ));
  }

  it('renders an input control', () => {
  // when
    renderTextField();

  // then
    screen.getByLabelText('Name');
  });

  context('when user types text', () => {
    it('calls the change handler', () => {
    // given
    renderTextField();

    // when
    fireEvent.change(screen.getByLabelText('Name'), {
      target: {
      value: 'New Name',
      },
    });

    // then
    expect(setText).toBeCalledWith('New Name');
    });
  });
});
```

반복되는 코드를 Extract Function하고, fireEvent 등을 통해 인터랙션만 검증합니다.\
만약 복잡한 로직이 컴포넌트로부터 분리된다면, 여기서는 이것만 검증하면 됩니다.

#### jest.mock()

`jest.mock()`는 자동으로 모듈을 mock 해줍니다.\
`jest.mock()` 함수는 첫 번째 인자로 넘어온 모듈 내의 모든 함수를 자동으로 목(mock) 함수로 바꿔줍니다.
단일 함수나 객체의 메소드를 모킹할 때 사용되는 `jest.fn()`과 다르게 모듈 전체를 모킹할 때 사용됩니다.

```jsx
// axios 모듈을 모킹하여 비어있는 객체로 대체
jest.mock('axios', () => ({}));

// 또는 모듈을 상대 경로로 지정하여 모킹
jest.mock('../exampleModule', () => ({
  functionName: jest.fn(),
}));
```

```jsx
jest.mock(moduleName, factory, options);
```

- `moduleName`: 가상(mock)으로 대체하려는 모듈의 경로입니다.
- `factory`  (선택적): 모듈을 대체할 가상 모듈의 구현 함수입니다.
- `options` (선택적): 모듈 대체의 추가적인 옵션을 설정하는 객체입니다.
  - `{virtual: true}` (기본값: false): 가상(mock) 모듈을 생성할 때 사용되는 가상 경로를 설정합니다. true로 설정하면 가상 경로를 사용하게 됩니다.
  - `{ value: true }`: 가상 모듈이 반환하는 값을 설정합니다. 특정 값을 테스트에서 명시적으로 반환하도록 할 때 사용됩니다.
  - `{ unmockedModulePathPatterns: ['/정규 표현식/'] })`: 일부 모듈은 실제로 대체되지 않도록 설정할 수 있는 정규표현식 패턴 목록입니다.
  - `{ virtualMocks: true }`: 모든 모듈을 가상으로 만들지 않고 특정 모듈만 가상으로 만들도록 설정할 수 있습니다.
  - `{ clearMocks: true }`: `jest.clearAllMocks()`가 호출될 때 해당 모듈의 모든 mock 함수를 자동으로 지우도록 설정합니다.

```jsx
// messeageService.js

export function sendEmail(email, message) {
  /* 이메일 보내는 코드 */
}

export function sendSMS(phone, message) {
  /* 문자를 보내는 코드 */
}
```

```jsx
import { register, deregister } from "./userService";
import { sendEmail, sendSMS } from "./messageService";

// messageService.js에서 넘어오는 sendEmail, sendSMS함수를 mock함수로 전환
jest.mock("./messageService");

beforeEach(() => {
  sendEmail.mockClear();
  sendSMS.mockClear();
});

const user = {
  email: "test@email.com",
  phone: "012-345-6789",
};

test("register sends messages", () => {
  register(user);

  expect(sendEmail).toHaveBeenCalledTimes(1);
  expect(sendEmail).toHaveBeenCalledWith(user.email, "회원 가입을 환영합니다!");

  expect(sendSMS).toHaveBeenCalledTimes(1);
  expect(sendSMS).toHaveBeenCalledWith(user.phone, "회원 가입을 환영합니다!");
});

test("deregister sends messages", () => {
  deregister(user);

  expect(sendEmail).toHaveBeenCalledTimes(1);
  expect(sendEmail).toHaveBeenCalledWith(user.email, "탈퇴 처리 되었습니다.");

  expect(sendSMS).toHaveBeenCalledTimes(1);
  expect(sendSMS).toHaveBeenCalledWith(user.phone, "탈퇴 처리 되었습니다.");
});
```

`jest.mock()`을 이용하여 외부 모듈 모킹을 할 수 있습니다.

```jsx
const axios = require("axios");
const userService = require("./userService");

// get을 포함한 axios 모든 함수가 목 함수로 자동으로 대체
jest.mock("axios");

test("findOne fetches data from the API endpoint and returns what axios get returns", async () => {
  axios.get.mockResolvedValue({
    data: {
      id: 1,
      name: "Dale Seo",
    },
  });

  const user = await userService.findOne(1);

  expect(user).toHaveProperty("id", 1);
  expect(user).toHaveProperty("name", "Dale Seo");
  expect(axios.get).toHaveBeenCalledTimes(1);
  expect(axios.get).toHaveBeenCalledWith(
    `https://jsonplaceholder.typicode.com/users/1`
  );
});
```

#### 예시3

만약 외부 의존성이 큰 코드를 작성한다면, 해당 부분만 가짜로 구현할 수 있습니다.

```jsx
// App.test.tsx

import { render, screen } from '@testing-library/react';

import App from './App';

// useFetchProduct 함수는 mock의 2번째 인자인 콜백함수의 반환값이 됨
// 즉 useFetchProduct mock은 배열을 반환
jest.mock('./hooks/useFetchProducts', () => () => [
  {
    category: 'Fruits', price: '$1', stocked: true, name: 'Apple',
  },
]);

test('App', () => {
  render(<App />);

  screen.getByText('Apple');
});
```

#### Test fixture

Test Fixture는 테스트를 수행하기 전에 필요한 상태나 환경을 설정하는 것을 의미합니다.
Test Fixture을 사용하여 mock에서 반환할 값들을 몰아줄 수도 있습니다.

```jsx
// App.test.tsx

import { render, screen } from "@testing-library/react"

import App from "./App"
import fixtures from "../fixtures"

jest.mock('./hooks/useFetchProduct', () => () => fixtures.products)

test('App', () => {
  render(<App />)

  screen.getByText('Apple');
})
```

```jsx
// ../fixtures/index.ts

import products from './products'

export default {
  products,
}
```

```jsx
// ../fixtures/products.ts

const products = [
  {
    category: 'Fruits', price: '$1', stocked: true, name: 'Apple',
  },
]

export default products;
```

또는 `__mock__` 폴더를 만드는 방법이 있습니다.\
`__mocks__` 폴더는 Jest에서 모듈을 자동으로 가상(mock) 모듈로 대체하는 기능을 지원하는 특별한 디렉토리입니다.\
`__mocks__` 폴더 내부에 실제 모듈과 동일한 경로에 위치한 가상 모듈을 만들면, Jest는 테스트 시에 해당 가상 모듈로 실제 모듈을 자동으로 대체합니다.

```jsx
// App.test.tsx

import { render, screen } from "@testing-library/react"

import App from "./App"

jest.mock('./hooks/useFetchProduct')

test('App', () => {
  render(<App />)

  screen.getByText('Apple');
})
```

`useFetchProduct`파일이 있는 폴더에 `__mocks__`폴더를 만들고 아래의 `useFetchProduct.ts`파일을 만듭니다.

```jsx
// hooks/__mocks__/useFetchProduct.ts'

import fixtures from "../../../fixtures";

const useFetchProduct = jest.fn(() => fixtures.products)

export default useFetchProduct;
```

일반적으론 백엔드와 소통하는 부분이 차지하는 비중이 큰데, 이 부분을 하나씩 가짜 구현으로 바꾸다 보면 어려울 때가 있다.\
이럴 땐 `MSW` 등 다른 대안을 고려해 봐야 합니다.

## 참고 자료

- [React Testing Library](https://github.com/testing-library/react-testing-library)
- [jest-dom](https://github.com/testing-library/jest-dom)
- [Jest의 jest.fn(), jest.spyOn()를 이용한 함수 모킹](https://www.daleseo.com/jest-fn-spy-on/)
- [Jest의 jest.mock()을 이용한 모듈 모킹](https://www.daleseo.com/jest-mock-modules/)
