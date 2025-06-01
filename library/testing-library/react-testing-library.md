# 리액트 테스트에 사용되는 도구들

## RTL(React Testing Library)

Testing Library의 일부로 테스트를 위한 가상 DOM을 생성하고 DOM과 상호 작용하기 위한 유틸리티도 제공합니다.\
예를 들어, 브라우저 없이 테스트를 진행하면 DOM에서 요소를 찾을 수 있거나 클릭 요소와 같은 상호 작용 할 때 가상 DOM이 필요합니다.\
그럴 때 가상 DOM이 원하는 대로 작동하는지 확인할 수도 있습니다.

## Jest, Vitest

테스트 러너이기 때문에 테스트를 찾고 실행하는 것과 테스트 통과 여부를 결정하는 역할을 합니다.

## Testing Library 기본 예제

`create-react-app`을 통해 생성한 react앱에 기본으로 작성되어 있는 테스트를 실행해보려고 합니다.

1. react앱을 생성합니다.

   ```bash
   // 새로운 react앱을 생성

   npx create-react-app
   ```

2. `npm test` 명령어를 통해 test를 실행합니다.

   ```json
   // package.json

   {
     "scripts": {
       "test": "react-scripts test"
     }
   }
   ```

3. 다음과 같은 화면에서 a를 입력하여 Jest의 Watch 모드를 실행합니다.

   ```bash
   No tests found related to files changed since last commit.
   Press `a` to run all tests, or run Jest with `--watchAll`.

   Watch Usage
   › Press a to run all tests.
   › Press f to run only failed tests.
   › Press q to quit watch mode.
   › Press p to filter by a filename regex pattern.
   › Press t to filter by a test name regex pattern.
   › Press Enter to trigger a test run.
   ```

4. 결과창을 확인하고 q를 입력하여 Jest의 Watch 모드를 종료합니다.

   ```bash
   PASS  src/App.test.js
     ✓ renders learn react link (16 ms)

   Test Suites: 1 passed, 1 total
   Tests:       1 passed, 1 total
   Snapshots:   0 total
   Time:        1.778 s, estimated 4 s
   Ran all test suites.

   Watch Usage: Press w to show more.
   ```

위의 프로세스로 어떤 것을 test 한 것인지 아래에서 살펴보겠습니다.

### 리엑트의 App.test.js 예시

create-react-app을 통해 react앱을 생성하면 기본적으로 `App.test.js`이라는 파일이 있습니다.
`App.test.js`은 `test`라는 함수를 가지고 있습니다.

```jsx
// App.test.js

import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders learn react link", () => {
  // RTL
  render(<App />); // App 컴포넌트 렌더링
  const linkElement = screen.getByText(/learn react/i); // 텍스트와 일치하는 요소를 찾기

  // Jest
  expect(linkElement).toBeInTheDocument();
});
```

테스트 함수에서, 첫 번째로 `react-testing-library`를 이용해서 `render` 메서드를 실행합니다.\
`render()` 메서드는 인수로 제공하는 JSX에 관한 가상 DOM을 생성하며, 생성된 가상 DOM에는 `screen` global 객체로 접근합니다.

`react-testing-library`를 이용해서 `screen`객체의 특정 Query 메서드를 사용해서 요소에 접근할 수 있습니다.\
예제에서는 `getByText` 메서드는 인수를 DOM에서 표시되는 모든 텍스트를 기반으로 요소를 찾습니다.

`expect` 부분은 `jest`를 이용하는 것이며 자세한 설명은 아래에 나올 단언 부분에 있습니다.

### 단언(Assertion)

테스트 성공과 실패의 원인이 되는 부분을 말합니다.
`App.test.js`에선 아래의 코드가 해당합니다.

```jsx
// expect(expect argument).matcher()
expect(linkElement).toBeInTheDocument();
```

- expect: 단언을 시작하는데 사용

- expect argument: 단언의 대상

- matcher: 단언의 타입
  (Jest DOM에서 가져옴)

단언은 Jest의 전역 메서드인 `expect`로 시작합니다.
`expect` 다음은 `Matcher`가 오게 됩니다.
`Matcher`는 단언의 유형으로 여러가지를 사용할 수 있습니다.
위의 코드는 Jest DOM의 `toBeInTheDocument`를 사용했으며 `toBeInTheDocument`는 해당 element가 document으며 에 존재하는지 확인할 때 사용합니다.

`toBeInTheDocument`는 가상 DOM에만 사용할 수 있으며 모든 노드에 적용할 수 있는 `toBe`와 `toHaveLength` 같은 매처도 있습니다.

`App.js`에는 `App.test.js`의 `getByText`로 찾는 `Learn React`가 있습니다.

```jsx
// App.js

import logo from "./logo.svg";
import "./App.css";

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          // 발견 Learn React
        </a>
      </header>
    </div>
  );
}

export default App;
```

## React Testing Library

```bash
npm install --save-dev @testing-library/user-event
```

### 주요 API

```jsx
import { render, screen, fireEvent } from "@testing-library/react";
```

- `render`
  : 인자로 넘어온 JSX(컴포넌트)의 가상 DOM을 생성합니다.

- `screen`
  : DOM에서 특정 영역을 선택하기 위한 다양한 쿼리 함수를 제공합니다.
  쿼리 함수를 사용해서 화면에 렌더링된 컴포넌트의 요소들에 접근할 수 있습니다.
  스크린을 사용하려면 글로벌 DOM 환경이 필요합니다.
  jest를 사용하는 경우 테스트 환경을 jsdom으로 설정하면 글로벌 DOM 환경이 제공됩니다.

- `fireEvent`
  : 쿼리 함수로 선택된 영역을 대상으로 특정 사용자 이벤트를 발생시키기 위해서 사용합니다.
  현재는 `fireEvent` 보다 아래에 나올 `userEvent`를 사용합니다.

### Queries

DOM 요소에 접근하고 상호작용하기 위한 함수 집합을 나타냅니다.\
테스트 코드에서 특정 DOM 요소를 선택하거나 확인하는 데 사용됩니다.

```jsx
command[All]ByQueryType
```

- command
  - get
  - query
  - find
- \[All]
  - 제외 or 포함
- QueryType
  - Role
  - AltText
  - Text
  - Form element
    - PlaceholderText
    - LabelText
    - DisplayValue

#### Summary Table

![](https://velog.velcdn.com/images/zimablue14/post/aa9dc04c-93a5-447e-9296-9ea356cbb786/image.png)

#### Single Element

- `get...`
  : 특정 조건에 해당하는 요소가 없으면 에러를 던지고, 하나의 요소가 존재하면 해당 요소를 반환합니다.
  여러 개의 요소가 존재하면 에러를 던집니다. (재시도 불필요)

- `query...`
  : 특정 조건에 해당하는 요소가 없으면 null을 반환하고, 하나의 요소가 존재하면 해당 요소를 반환합니다.
  여러 개의 요소가 존재해도 첫 번째 요소만 반환합니다. (재시도 불필요)

- `find...`
  : 프로미스를 반환합니다. 이 프로미스는 특정 조건에 해당하는 요소가 나타날 때까지 기다린 후, 해당 요소를 반환합니다.
  여러 개의 요소가 존재해도 첫 번째 요소만 반환합니다. (비동기로 재시도)
  이 프로미스는 요소를 찾으면 해결(resolve)되며, 찾지 못하거나 기본 타임아웃인 1000ms 이후에도 찾지 못하면 거부(reject)됩니다.

```jsx
test("", async () => {
  render(<UserList user={users} />);
  screen.debug();
  const titleEl = await screen.findByRole(
    "heading",
    {
      name: "사용자 목록",
    },
    {
      timeout: 2000,
    }
  );
  // 실패할 경우 이전 debug와의 차이를 보며 실패한 이유를 확인하기 위해 사용
  screen.debug();
  expect(titleEl).toBeInTheDocument();
});
```

#### Multiple Elements

- `getAllBy...`
  : 특정 조건에 해당하는 모든 요소를 배열로 반환하며, 요소가 없으면 에러를 던집니다. (재시도 불필요)

- `queryAllBy...`
  : 특정 조건에 해당하는 모든 요소를 배열로 반환하며, 요소가 없으면 빈 배열을 반환합니다. (재시도 불필요)

- `findAllBy...`
  : 프로미스를 반환합니다. 이 프로미스는 특정 조건에 해당하는 모든 요소가 나타날 때까지 기다린 후, 요소들을 배열로 반환하며, 요소가 없으면 에러를 던집니다. (비동기로 재시도)
  프로미스가 찾아진 요소들의 배열로 해결(resolve)되며, 요소를 찾지 못하거나 기본 타임아웃인 1000ms 이후에도 찾지 못하면 거부(reject)됩니다.
  또한, findBy 시리즈의 함수들은 getBy\* 쿼리와 waitFor 함수의 조합으로 생각할 수 있습니다. 즉, 해당 요소를 찾을 때까지 대기하고, 찾은 후에는 반환됩니다. waitFor 옵션은 마지막 인자로 전달되며, 특정 상황이나 조건을 대기할 때 사용됩니다.

#### ByLabelText

주어진 텍스트와 일치하는 label 또는 aria-label을 가진 요소를 찾습니다.

```jsx
// JSX 예시
<label htmlFor="username">Username:</label>
<input id="username" aria-label="Username" />

// Testing Library Queries
const element = screen.getByLabelText('Username');
```

#### ByPlaceholderText

주어진 입력 placeholder 값과 일치하는 요소를 찾습니다.

```jsx
// JSX 예시
<input placeholder="Enter your email" />;

// Testing Library Queries
const element = screen.getByPlaceholderText("Enter your email");
```

#### ByText

주어진 텍스트와 일치하는 요소를 찾습니다.

```jsx
// JSX 예시
<div>Hello, World!</div>;

// Testing Library Queries
const element = screen.getByText("Hello, World!");
```

#### ByDisplayValue

주어진 폼 요소의 현재 값과 일치하는 요소를 찾습니다.

```jsx
// JSX 예시
<input type="text" value="zima" />;

// Testing Library Queries
const element = screen.getByDisplayValue("zima");
```

#### ByAltText

주어진 이미지 alt 속성 값과 일치하는 이미지 요소를 찾습니다.

```jsx
// JSX 예시
<img src="image.jpg" alt="A beautiful landscape" />;

// Testing Library Queries
const element = screen.getByAltText("A beautiful landscape");
```

#### ByTitle

주어진 title 속성 값과 일치하는 요소를 찾습니다.

```jsx
// JSX 예시
<svg>
  <title>Important Information</title>
  <circle cx="50" cy="50" r="30" />
</svg>;

// Testing Library Queries
const element = screen.getByTitle("Important Information");
```

#### ByRole

주어진 aria role과 일치하는 요소를 찾습니다.

[WAI-ARIA Roles](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles)

- `<h1>` ~ `<h6>`: "heading"
- `<button>`: "button"
- `<a>`: "link"
- `<checkbox>`: "checkbox"
- `<radio>`: "radio"
- `<select>`: "combobox"
- `<img>`: "img"
- `<textarea>`: "textbox"
- `<input>` (type이 search인 경우): "searchbox"
- `<input>` (type이 range인 경우): "slider"
- `<progress>`: "progressbar"
- `<ul>`, `<ol>`: "list"
- `<li>`: "listitem"
- `<nav>`: "navigation"
- `<menu>`: "menu"
- `<form>`: "form"
- `<textarea>`, `<input>` (type이 text, password, email, tel, url, number인 경우): "textbox"
- `<input>` (type이 checkbox, radio, submit, reset, button인 경우): 각각 "checkbox", "radio", "button"

```jsx
// JSX 예시
<button>Close</button>;

// Testing Library Queries
// name은 text 노드를 가진 요소를 선택
const button = screen.getByRole("button", { name: "Close" });
```

`<img />`의 경우 `name`은 `alt`를 나타냅니다.

#### ByTestId

주어진 data-testid 속성 값과 일치하는 요소를 찾습니다.

```jsx
// JSX 예시
<button data-testid="submit-button">Submit</button>;

// Testing Library Queries
const button = screen.getByTestId("submit-button");
```

#### 어떤 쿼리를 사용해야 할까요?

테스트는 사용자들이 소프트웨어를 사용하는 방식을 모방해야 합니다.

1. Queries Accessible to Everyone
   : 화면을 쳐다보고 있는 사람에게든 스크린 리더 등의 보조 기술을 사용하고 있는 사람이건 간에 모두가 액세스할 수 있는 쿼리를 사용하는 게 좋습니다.
   ex) `getByRole`, `getByLabelText`, `getByPlaceholderText`, `getByDisplayValue`

2. Semantic Queries
   : 브라우저와 보조 기술의 사이의 일관성이 다소 떨어지기 때문에 잘 선호되지 않습니다.
   ex) `getByAltText`, `getByTitle`

   - 어떤 브라우저는 alt 속성을 이미지 설명으로만 사용하는 반면, 다른 브라우저는 도움말 텍스트로도 사용할 수 있습니다. 어떤 보조 기술은 alt 속성을 읽어주는 반면, 다른 보조 기술은 무시할 수 있습니다.
   - 어떤 브라우저는 title 속성을 툴팁으로 표시하는 반면, 다른 브라우저는 무시할 수 있습니다. 어떤 보조 기술은 title 속성을 읽어주는 반면, 다른 보조 기술은 무시할 수 있습니다.

3. Test IDs
   : 사용자들이 테스트 ID와 상호작용할 일은 절대 없기 때문에 최후의 수단으로만 사용되어야 합니다.
   ex) `getByTestId`

### Custom matchers

특정 라이브러리나 프레임워크에서 제공하거나 사용자가 직접 만들어 추가할 수 있는 매처들입니다.
특정 도메인이나 컴포넌트 라이브러리에 특화된 검증을 수행하는 데 사용됩니다.

#### React Testing Library와 함께 사용하는 jest-dom 패키지가 제공하는 custom matchers

- `toBeDisabled`
  : 대상 요소가 비활성화(disabled)되었는지 확인합니다.

- `toBeEnabled`
  : 대상 요소가 활성화(enabled)되었는지 확인합니다.

- `toBeEmptyDOMElement`
  : 대상 DOM 요소가 비어있는지 확인합니다.

- `toBeInTheDocument`
  : 대상 DOM 요소가 실제 DOM에 존재하는지 확인합니다.

- `toBeInvalid`
  : 대상 요소가 유효하지 않은 상태(invalid)인지 확인합니다.

- `toBeRequired`
  : 대상 요소가 필수(required)인지 확인합니다.

- `toBeValid`
  : 대상 요소가 유효(valid)한 상태인지 확인합니다.

- `toBeVisible`
  : 대상 요소가 화면에 보이는(visible)지 확인합니다.

- `toContainElement`
  : 대상 요소가 특정 자식 요소를 포함하고 있는지 확인합니다.

- `toContainHTML`
  : 대상 요소가 특정 HTML을 포함하고 있는지 확인합니다.

- `toHaveAccessibleDescription`
  : 대상 요소에 접근 가능한 설명이 있는지 확인합니다.

- `toHaveAccessibleErrorMessage`
  : 대상 요소에 접근 가능한 오류 메시지가 있는지 확인합니다.

- `toHaveAccessibleName`
  : 대상 요소에 접근 가능한 이름이 있는지 확인합니다.

- `toHaveAttribute`
  : 대상 요소가 특정 속성을 가지고 있는지 확인합니다.

- `toHaveClass`
  : 대상 요소가 특정 클래스를 가지고 있는지 확인합니다.

- `toHaveFocus`
  : 대상 요소가 포커스를 가지고 있는지 확인합니다.

- `toHaveFormValues`
  : 대상 요소가 특정 폼 값들을 가지고 있는지 확인합니다.

- `toHaveStyle`
  : 대상 요소가 특정 스타일을 가지고 있는지 확인합니다.

- `toHaveTextContent`
  : 대상 요소가 특정 텍스트 내용을 가지고 있는지 확인합니다.

- `toHaveValue`
  : 대상 요소의 값이 특정 값과 일치하는지 확인합니다.

- `toHaveDisplayValue`
  : 대상 요소가 특정 표시 값과 일치하는지 확인합니다.

- `toBeChecked`
  : 대상 요소가 체크(checked)된 상태인지 확인합니다.

- `toBePartiallyChecked`
  : 대상 요소가 부분적으로 체크된 상태인지 확인합니다.

- `toHaveRole`
  : 대상 요소가 특정 역할(role)을 가지고 있는지 확인합니다.

- `toHaveErrorMessage`
  : 대상 요소가 특정 에러 메시지를 가지고 있는지 확인합니다.

### Deprecated Matchers

이전 버전에서 사용되었지만 현재 버전에서는 권장되지 않거나 더 나은 대안이 있어서 폐기된 매처들입니다.
특별한 이유 없이는 사용을 지양하고, 대신에 더 효과적이고 가독성 높은 Matchers를 사용하는 것이 좋습니다.

### User Event 라이브러리

fireEvent는 DOM 이벤트를 발생시키는데 `user-event`는 인터랙션 전체를 시뮬레이트합니다.

#### userEvent.setup()

`userEvent.setup()`의 도입은 v.14 에서 제공되는 새로운 기능 중 하나입니다.\
`userEvent`는 클릭, 키보드 이벤트 등의 다양한 이벤트를 실제 브라우저에서의 동작과 유사하게 시뮬레이션 할 수 있는 라이브러리입니다.\
초기 설정 및 설정된 사용자 상호 작용에 대한 인스턴스를 반환하여 여러 테스트 간에 상태가 공유되지 않고 독립적으로 실행될 수 있으며, 보다 표현적이고 가독성 있게 테스트 코드를 작성할 수 있도록 도와줍니다.

```jsx
import userEvent from "@testing-library/user-event";

const user = userEvent.setup();
```

`user-event` API는 항상 프라미스를 반환하기 때문에 비동기 처리를 해줘야 합니다.
해당 메서드가 완료될 때까지 기다렸다가 단언을 수행해야 합니다.

```jsx
// async
test("Checkbox enables button", async () => {
  const user = userEvent.setup();

  render(<SummaryForm />);

  const checkbox = screen.getByRole("checkbox", {
    name: /terms and conditions/i,
  });
  const confirmButton = screen.getByRole("button", { name: /confirm order/i });

  // await
  await user.click(checkbox);
  expect(confirmButton).toBeEnabled();
});
```

#### Pointer events

클릭할 수 있는 요소가 아닌 `div`나 `span` 등의 다른 태그로 기능과 모양을 흉내내어 구현되어있는 요소를 클릭해야 할 때가 있습니다.

[pointer-events](https://developer.mozilla.org/en-US/docs/Web/CSS/pointer-events)는 그래픽 요소가 포인터 이벤트의 대상이 될 수 있는 상황(있는 경우)에 대해 나타내는데, 흉내내어 구현되어 있는 요소는 `pointer-events: none;`속성으로 되어 있습니다.

`pointer-events: none;`인 요소에는 `click` 이벤트를 사용해도 클릭할 수 있는 요소가 아니라는 에러가 발생합니다.

그런 경우 `userEvent`에서 제공하는 `pointerEventsCheck`옵션을 사용하여 해결할 수 있습니다.

```jsx
const user = userEvent.setup({
  pointerEventsCheck: PointerEventsCheckLevel.Never,
});
```

`pointerEventsCheck` 옵션을 `PointerEventsCheckLevel.Never`로 설정하면, `user-event`가 요소의 "pointer-events" 속성을 무시하고 클릭을 시도합니다.

추가적인 내용은 [pointerEventsCheck](https://testing-library.com/docs/user-event/options/)에서 확인할 수 있습니다.

#### click(element, eventInit, options)

주어진 엘리먼트를 클릭하는 행동을 시뮬레이션하는 데 사용됩니다.

- `element`
  : 클릭할 대상 엘리먼트입니다.

- `eventInit`
  : 이벤트 초기화 객체로, [MouseEvent](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent/MouseEvent) 인터페이스에서 지원되는 속성을 포함합니다.
  클릭 이벤트에 대한 세부 정보를 지정하려는 경우 사용됩니다.

- `options`
  : `user-event`에서 제공하는 여러 동작을 설정하기 위한 옵션 객체입니다.
  여기에는 특정 동작을 조정하는 데 사용되는 여러 속성이 있습니다.
  세부 항목은 [Options](https://testing-library.com/docs/user-event/options/)에서 확인할 수 있습니다.

- Pointer events options
  : 위의 링크 [Options](https://testing-library.com/docs/user-event/options/)에 해당하는 내용입니다.
  만약 클릭하려는 요소가 "pointer-events: none"으로 되어 있으면, 일반적으로는 클릭이 무시되고 에러가 발생합니다.
  그렇기 때문에 요소를 클릭하려는 시도에서 에러가 발생하지 않고 무시하고자 할 때 `skipPointerEventsCheck: true`라는 설정을 사용할 수 있습니다.
  설정은 클릭뿐만 아니라 `dblClick`, `hover`, `unhover`, `selectOptions`, `deselectOptions` 등과 같은 다양한 상호작용에도 적용됩니다.

```jsx
userEvent.click(elem, undefined, { skipPointerEventsCheck: true });
```

#### dblClick(element, eventInit, options)

해당 엘리먼트를 더블 클릭합니다.

- `element`: 더블 클릭을 수행할 대상 엘리먼트입니다.

- `eventInit`
  : 이벤트 초기화 객체로, [MouseEvent](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent/MouseEvent) 인터페이스에서 지원되는 속성을 포함합니다.
  클릭 이벤트에 대한 세부 정보를 지정하려는 경우 사용됩니다.

- `options`
  : `user-event`에서 제공하는 여러 동작을 설정하기 위한 옵션 객체입니다.
  여기에는 특정 동작을 조정하는 데 사용되는 여러 속성이 있습니다.
  세부 항목은 [Options](https://testing-library.com/docs/user-event/options/)에서 확인할 수 있습니다.

#### type(element, text, options)

`<input>` 또는 `<textarea>` 엘리먼트에 텍스트를 입력하는 동작을 시뮬레이트합니다.
키보드 이벤트를 연속적으로 발생시켜 엘리먼트에 텍스트를 입력하는 것을 흉내 냅니다.
주로 테스트 케이스에서 사용자의 입력 상황을 모방하는 데 유용합니다.

- `element`
  : 텍스트를 입력할 대상 엘리먼트입니다.

- `text`
  : 입력할 텍스트입니다.
  이 텍스트가 해당 엘리먼트에 입력됩니다.

- `options`
  : `user-event`에서 제공하는 여러 동작을 설정하기 위한 옵션 객체입니다.
  여기에는 특정 동작을 조정하는 데 사용되는 여러 속성이 있습니다.
  세부 항목은 [Options](https://testing-library.com/docs/user-event/options/)에서 확인할 수 있습니다.

- Special characters
  : 특수한 텍스트 문자열을 사용하여 특수한 키 이벤트를 발생시킬 수 있습니다.
  자세한 내용은 [Special characters](https://testing-library.com/docs/user-event/v13/#special-characters)에서 확인할 수 있습니다.

아래의 예제에서는 엔터 키를 눌러 새로운 줄을 생성합니다.

```jsx
// 두 항복은 동일한 결과를 생성합니다.
userEvent.type(element, "Hello,{space}World!");
userEvent.type(element, "Hello, World!");
```

- `{enter}`
  : Enter 키를 누릅니다.
  `<textarea>`에서만 새로운 줄을 삽입합니다.

- `{space}`
  : 스페이스 바를 입력합니다.

- `{esc}`
  : Escape 키를 누릅니다.

- `{backspace}`
  : Backspace 키를 누릅니다.
  [selectedRange](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement/setSelectionRange) 내의 문자를 삭제할 수 있습니다.
  현재 커서 위치에서 이전 문자를 삭제하는 동작을 나타냅니다.

- `{del}`: Delete 키를 누릅니다.
  [selectedRange](https://developer.mozilla.org/en-US/docs/Web/API/HTMLInputElement/setSelectionRange) 내의 문자를 삭제할 수 있습니다.
  {del}은 현재 커서 위치에서 다음 문자를 삭제하는 동작을 나타냅니다.

- `{selectall}`
  : 특정 엘리먼트의 텍스트를 모두 선택하는 동작을 나타냅니다.
  텍스트 입력이 가능한 일반적인 텍스트 상자(`<input type="text">` 또는 `<textarea>`) 등에는 잘 작동하지만, 이메일, 비밀번호, 숫자 등 텍스트 선택이 불가능한 경우에는 이 동작이 올바르게 작동하지 않을 수 있습니다.

- `{arrowleft}`
  : 왼쪽 화살표 키를 누릅니다.

- `{arrowright}`
  : 오른쪽 화살표 키를 누릅니다.

- `{arrowup}`
  : 위쪽 화살표 키를 누릅니다.

- `{arrowdown}`
  : 아래쪽 화살표 키를 누릅니다.

- `{home}`
  : Home 키를 누릅니다.

- `{end}`
  : End 키를 누릅니다.

- `{shift}`
  : Shift 키를 누릅니다.
  (이 특수 문자가 입력된 이후에 대문자로 변환되지 않습니다.)
  일반적으로 Shift 키를 누르면 알파벳 문자를 입력할 때 대문자로 변환됩니다.
  하지만 `{shift}`가 입력된 이후에는 대문자로 변환되지 않습니다.

- `{ctrl}`
  : Control 키를 누릅니다.
  (Modifier: ctrlKey)

- `{alt}`
  : Alt 키를 누릅니다.
  (Modifier: altKey)

- `{meta}`
  : OS(예: Windows 키 또는 Command 키) 키를 누릅니다.
  (Modifier: metaKey)

- `{capslock}`
  : Caps Lock 키를 누릅니다.
  Caps Lock을 활성화하는 것처럼 동작하며, 사용 중인 운영 체제에 따라 다르게 동작할 수 있습니다.
  `{capslock}`은 키가 눌릴 때(keydown)와 키가 떼어질 때(keyup) 양쪽 이벤트를 발생시키며, 이는 사용자가 실제로 Caps Lock 키를 클릭하여 Caps Lock을 활성화하는 것을 모방합니다.
  Caps Lock 키가 활성화되면 눌린 키는 대문자로 인식되므로, `{capslock}`을 사용하면 이 동작을 테스트할 수 있습니다.

#### keyboard(text, options)

주어진 텍스트에 해당하는 키보드 이벤트를 시뮬레이션합니다.

- `text`
  : 시뮬레이션할 키보드 입력.

- `options`
  : `user-event`에서 제공하는 여러 동작을 설정하기 위한 옵션 객체입니다.
  여기에는 특정 동작을 조정하는 데 사용되는 여러 속성이 있습니다.
  세부 항목은 [keyboardMap](https://testing-library.com/docs/user-event/options/#keyboardmap)에서 확인할 수 있습니다.

- 예시

```jsx
userEvent.keyboard("foo"); // f, o, o로 변환됩니다.

// 중괄호 {와 대괄호 \[가 특수 문자로 사용되며, 이를 참조하려면 두 번 입력해야 합니다
userEvent.keyboard("{{a[["); // {, a, [로 변환됨

// 키가 눌려진 상태를 유지하지 않고 Shift 는 f 를 누르기 이전에 입력이 풀립니다.
userEvent.keyboard("{Shift}{f}{o}{o}"); // Shift, f, o, o로 변환됨

//특수 문자열은 해당 문자열 앞에 \ 백슬래쉬를 붙여 이스케이프 할 수 있습니다.
keyboard("{\\}}"); // translates to: }

userEvent.keyboard("[ShiftLeft][KeyF][KeyO][KeyO]"); // translates to: Shift, f, o, o

userEvent.keyboard("{shift}{ctrl/}a{/shift}"); // translates to: Shift(down), Control(down+up), a, Shift(up)

// 문자열 끝에 > 를 추가하여 키를 계속 누를 수 있습니다.
userEvent.keyboard("{Shift>}A{/Shift}"); // translates to: Shift(down), A, Shift(up)

keyboard("{a>}"); // 누른 상태를 유지
keyboard("{a>5}"); // 누른 상태를 유지해 keydown 이벤트를 5번 트리거
keyboard("{a>5/}"); // keydown 이벤트를 다섯번 트리거하고 키를 해제
```

#### upload(element, file, [options])

`<input type="file">` 엘리먼트에 파일을 업로드하는 동작을 시뮬레이트합니다. 이 함수를 사용하여 파일을 업로드하거나, 다중 파일 업로드를 수행할 수 있습니다.

- `element`
  : 텍스트를 입력할 대상 엘리먼트입니다.

- `file`
  : 업로드할 파일 또는 파일 배열.

- `options`
  : `user-event`에서 제공하는 여러 동작을 설정하기 위한 옵션 객체입니다.
  여기에는 특정 동작을 조정하는 데 사용되는 여러 속성이 있습니다.
  세부 항목은 [applyAccept](https://testing-library.com/docs/user-event/options/#applyaccept)에서 확인할 수 있습니다.
  `applyAccept: true`로 설정하면 accept 속성을 적용하고 매치하지 않는 파일은 폐기됩니다.

#### clear(element)

`<input>` 또는 `<textarea>` 엘리먼트의 텍스트를 선택하고 삭제하는 작업을 시뮬레이트합니다.
이 함수를 사용하여 텍스트를 지우고, 그 결과를 확인할 수 있습니다.

#### selectOptions(element, values, options)

`<select>` 또는 `<select multiple>` 엘리먼트에서 지정된 옵션을 선택하는 동작을 시뮬레이트합니다.
이 함수는 다음과 같은 방법으로 사용될 수 있습니다.

- `element`
  : 텍스트를 입력할 대상 엘리먼트입니다.

- `values`
  : 선택할 옵션 값 또는 값 배열.

- `options`
  : `user-event`에서 제공하는 여러 동작을 설정하기 위한 옵션 객체입니다.
  여기에는 특정 동작을 조정하는 데 사용되는 여러 속성이 있습니다.
  세부 항목은 [Options](https://testing-library.com/docs/user-event/options/)에서 확인할 수 있습니다.

#### deselectOptions(element, values, options)

`<select multiple>` 엘리먼트에서 특정 옵션의 선택을 해제하는 동작을 모의로 수행합니다. 이 함수는 다음과 같이 사용될 수 있습니다.

- `element`
  : 텍스트를 입력할 대상 엘리먼트입니다.

- `values`
  : 선택 해제할 옵션 값 또는 값 배열.

- `options`
  : `user-event`에서 제공하는 여러 동작을 설정하기 위한 옵션 객체입니다.
  여기에는 특정 동작을 조정하는 데 사용되는 여러 속성이 있습니다.
  세부 항목은 [Options](https://testing-library.com/docs/user-event/options/)에서 확인할 수 있습니다.

```jsx
// 여러 옵션 동시에 선택 해제도 가능
// userEvent.deselectOptions(screen.getByRole('listbox'), ['1', '2'])
```

#### tab(options)

탭 키 이벤트를 발생시켜 문서에서 [document.activeElement](https://developer.mozilla.org/ko/docs/Web/API/Document/activeElement)를 변경하는 동작을 모의로 수행합니다.
이 함수는 브라우저에서 사용자가 탭 키를 눌렀을 때와 동일한 동작을 시뮬레이트합니다.

- options
  : Tab 이벤트 옵션.

- `shift`
  : 기본값은 false이며, true로 설정하면 탭의 방향이 반대로 변경됩니다.

- `focusTrap`
  : 기본값은 `document`이며, 탭 이벤트가 제한되는 컨테이너 엘리먼트를 지정할 수 있습니다.

#### hover(element, options)

주어진 엘리먼트 위에 마우스 호버 이벤트를 발생시키는 역할을 합니다.
이 함수를 사용하면 특정 엘리먼트에 마우스가 호버되었을 때의 동작을 시뮬레이트하여 테스트를 수행할 수 있습니다.

- `options`
  : `user-event`에서 제공하는 여러 동작을 설정하기 위한 옵션 객체입니다.
  여기에는 특정 동작을 조정하는 데 사용되는 여러 속성이 있습니다.
  세부 항목은 [Options](https://testing-library.com/docs/user-event/options/)에서 확인할 수 있습니다.

#### unhover(element, options)

특정 엘리먼트에서 마우스 호버를 제거하는 역할을 합니다.
이 함수를 사용하면 호버된 상태의 엘리먼트에서 마우스가 벗어났을 때의 동작을 시뮬레이트하여 테스트를 수행할 수 있습니다.

- `options`
  : `user-event`에서 제공하는 여러 동작을 설정하기 위한 옵션 객체입니다.
  여기에는 특정 동작을 조정하는 데 사용되는 여러 속성이 있습니다.
  세부 항목은 [Options](https://testing-library.com/docs/user-event/options/)에서 확인할 수 있습니다

#### paste(element, text, eventInit, options)

사용자가 입력란에 텍스트를 붙여넣는 것을 시뮬레이트하는 데 사용됩니다.
이 함수를 사용하면 특정 엘리먼트에 텍스트를 붙여넣는 테스트를 수행할 수 있습니다.

- `text`
  : 붙여넣을 텍스트.

- `eventInit`
  : 붙여넣기 이벤트에 대한 초기 설정으로, 주로 clipboardData와 관련된 설정을 제공하는 데 사용됩니다.

- `options`
  : `user-event`에서 제공하는 여러 동작을 설정하기 위한 옵션 객체입니다.
  여기에는 특정 동작을 조정하는 데 사용되는 여러 속성이 있습니다.
  세부 항목은 [Options](https://testing-library.com/docs/user-event/options/)에서 확인할 수 있습니다

## 설정

### src/setupTests.js

```javascript
// 각 테스트가 DOM에 렌더링해놓은 내용들을 테스트가 끝날 때 다음 테스트를 위해서 지워주기
import "@testing-library/react/cleanup-after-each";

// jest-dom가 제공하는 matcher를 Jest 테스트 러너에게 인식
import "@testing-library/jest-dom/extend-expect";
```

## `render`

```ts
function render(
  ui: React.ReactElement<any>,
  options?: {
    /* 옵션에 대한 자세한 설명은 아래 참고 */
  }
): RenderResult;
```

`render()`는 컴포넌트를 `document.body`에 추가된 `container`에 렌더링합니다.

### 사용 예

```ts
import { render } from "@testing-library/react";

render(<div />);
```

```ts
import { render } from "@testing-library/react";
import "@testing-library/jest-dom";

test("메시지를 렌더링한다", () => {
  const { asFragment, getByText } = render(<Greeting />);

  expect(getByText("Hello, world!")).toBeInTheDocument();
  expect(asFragment()).toMatchInlineSnapshot(`
    <h1>Hello, World!</h1>
  `);
});
```

## `render` 옵션 설명

> 대부분의 경우 옵션을 지정할 필요는 없지만, 필요할 경우 아래와 같은 옵션들을 사용할 수 있습니다.

| 옵션 이름            | 설명                                                                                                                                                           | 기본값          |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------- |
| `container`          | 기본적으로 React Testing Library는 div를 생성해 `document.body`에 추가하여 렌더링합니다. <br> 직접 `HTMLElement`를 지정하면 자동으로 body에 추가되지 않습니다. | `document.body` |
| `baseElement`        | 쿼리와 디버그 출력의 기준이 되는 루트 요소입니다. container가 지정되면 이를 따릅니다.                                                                          | `document.body` |
| `hydrate`            | `true`로 설정하면 `ReactDOM.hydrate`로 렌더링합니다. 서버사이드 렌더링과 함께 사용할 수 있습니다.                                                              | `false`         |
| `legacyRoot`         | React 18 이상에서만 사용 가능합니다. Concurrent 모드 대신 React 17 방식으로 렌더링하려면 사용합니다.                                                           | `false`         |
| `onCaughtError`      | React가 Error Boundary에서 에러를 잡았을 때 호출되는 콜백 함수입니다.                                                                                          | 없음            |
| `onRecoverableError` | React가 자동으로 복구 가능한 에러를 처리할 때 호출됩니다.                                                                                                      | 없음            |
| `wrapper`            | 테스트 대상 컴포넌트를 감싸는 컴포넌트입니다. <br>공통 Provider를 넣을 때 유용합니다.                                                                          | `null`          |
| `queries`            | 기본 쿼리를 커스터마이징할 수 있습니다. 예: 테이블 전용 쿼리 추가 등                                                                                           | DOM 쿼리 기본값 |
| `reactStrictMode`    | `<StrictMode>`로 감쌀지 여부를 지정합니다. `configure`로 지정된 값을 덮어쓸 수 있습니다.                                                                       | `false`         |

예시:

```ts
const table = document.createElement("table");

const { container } = render(<TableBody {...props} />, {
  container: document.body.appendChild(table),
});
```

## `render` 반환값 (Render Result)

- `render()`는 다음과 같은 속성들을 가진 객체를 반환합니다.

### ...queries

DOM Testing Library의 쿼리 함수들을 기본적으로 제공합니다. `baseElement`를 기준으로 작동합니다.

```ts
const { getByLabelText, queryAllByTestId } = render(<Component />);
```

### container

컴포넌트가 렌더링된 HTML 요소 (`ReactDOM.render` 기준). 일반적으로 `<div>`이며, `querySelector()` 등으로 탐색할 수 있습니다.

> ⚠️ 너무 자주 container를 직접 탐색하는 대신, 쿼리 API를 사용하는 것이 좋습니다.

### baseElement

`render()`로 렌더링된 루트 요소의 기준입니다. 기본은 `document.body`이며, 쿼리와 `debug()`의 기준이 됩니다.

### debug

`console.log(prettyDOM(baseElement))`를 실행하는 편의 함수입니다.

```ts
const HelloWorld = () => <h1>Hello World</h1>;
const { debug } = render(<HelloWorld />);
debug(); // <div><h1>Hello World</h1></div>
```

### rerender

다른 props로 같은 컴포넌트를 다시 렌더링할 수 있습니다.

```ts
const { rerender } = render(<NumberDisplay number={1} />);
rerender(<NumberDisplay number={2} />);
```

### unmount

컴포넌트를 언마운트하여 DOM에서 제거합니다. 메모리 누수 방지나 언마운트 후 상태 확인에 유용합니다.

```ts
const { unmount } = render(<Login />);
unmount(); // container.innerHTML === ''
```

### asFragment

렌더링된 결과를 DocumentFragment로 반환합니다. snapshot-diff 등과 함께 유용하게 사용됩니다.

```ts
const { asFragment, getByText } = render(<TestComponent />);
const firstRender = asFragment();
fireEvent.click(getByText(/Click/));
expect(firstRender).toMatchDiffSnapshot(asFragment());
```

## `cleanup`

- `render()`로 마운트된 React 트리를 해제합니다.
- 대부분의 프레임워크(Jest, Mocha 등)는 `afterEach()`로 자동 호출해줍니다.
- 그렇지 않은 프레임워크(예: ava)에서는 수동으로 호출해야 합니다.

```ts
import { cleanup, render } from "@testing-library/react";
import test from "ava";

test.afterEach(cleanup);

test("DOM에 렌더링된다", () => {
  render(<div />);
});
```

> 호출하지 않으면 메모리 누수나 테스트 간 간섭이 발생할 수 있습니다.

## `act`

- React의 `act()` 함수를 감싼 얇은 래퍼입니다.

- act 함수는 상호 작용(렌더링, 이펙트 등..)을 함께 그룹화하고 실행해 렌더링과 업데이트가 실제 앱이 동작하는 것과 유사한 방식으로 동작합니다.

- 즉, 테스트 환경에서 `act`를 사용하면 가상의 돔(jsdom)에 제대로 반영되었다는 가정하에 테스트가 가능해집니다.

- 컴포넌트를 렌더링한 뒤 업데이트 하는 코드의 결과를 검증하고 싶을 때 사용합니다.

- 쉽게 말하면 테스트할 때 "화면이 바뀌는 일이 다 끝났는지 확인하고 검사해" 라고 React에게 알려주는 함수입니다.

- 모든 `render`, 이벤트(`user-event`)는 내부적으로 이미 `act()`로 감싸져 있으므로 직접 사용할 필요는 거의 없습니다.

- 하지만 `render`, 이벤트(`user-event`) 이외에서는 테스트 환경에서 컴포넌트 렌더링 결과를 jsdom에 반영하기 위해 `act()` 함수를 반드시 호출해야 합니다.

### `act`예시

- 외부 상태 라이브러리(Zustand, Redux 등)를 직접 업데이트하거나

- React 외부에서 발생하는 비동기 작업 이후 상태를 검증할 때

- `setTimeout`, `Promise.then` 등 렌더 외부 이벤트

#### ❌ act() 없이 검사

```tsx
const [count, setCount] = useState(0);

setCount(1); // 렌더 함수나 유저 이벤트 없이 상태 변경

expect(screen.getByText("1")).toBeInTheDocument(); // 아직 0일 수도 있음
```

#### ✅ act() 사용하여 검사

```tsx
act(() => {
  setCount(1); // 렌더 함수나 유저 이벤트 없이 상태 변경
});

expect(screen.getByText("1")).toBeInTheDocument(); // 이제 제대로 검사 가능
```

## `renderHook`

- 커스텀 훅을 테스트할 수 있도록 도와주는 도구입니다.
- 훅 자체를 테스트하는 라이브러리나 유틸 구현에 적합합니다.

```ts
const { result } = renderHook(() => useLoggedInUser());
expect(result.current).toEqual({ name: "Alice" });
```

### 옵션: `initialProps`

- 초기 props를 `render` 콜백에 전달할 수 있습니다.

```ts
const { result, rerender } = renderHook((props = {}) => props, {
  initialProps: { name: "Alice" },
});
expect(result.current).toEqual({ name: "Alice" });

rerender();
expect(result.current).toEqual({ name: undefined });
```

### wrapper에 props 전달하는 방법:

```ts
const createWrapper = (Wrapper, props) => {
  return function CreatedWrapper({ children }) {
    return <Wrapper {...props}>{children}</Wrapper>;
  };
};

{
  wrapper: createWrapper(Wrapper, { value: "foo" });
}
```

### 기타 옵션

- `wrapper`: `render`와 동일
- `reactStrictMode`: `render`와 동일

### 반환값

| 속성       | 설명                                                                    |
| ---------- | ----------------------------------------------------------------------- |
| `result`   | 현재 렌더링된 훅의 반환값. `result.current`로 접근하여 최신 상태를 추적 |
| `rerender` | 새로운 props로 훅을 다시 호출하여 상태를 갱신                           |
| `unmount`  | 훅 제거                                                                 |

## `configure`

- 테스트 전역 설정을 변경합니다.

```ts
import { configure } from "@testing-library/react";

configure({ reactStrictMode: true });
```

| 옵션 이름         | 설명                                         |
| ----------------- | -------------------------------------------- |
| `reactStrictMode` | `<StrictMode>`로 감쌀지 여부 (기본값: false) |

## ✅ 정리

| 항목           | 설명                                          |
| -------------- | --------------------------------------------- |
| `render()`     | 컴포넌트를 DOM에 렌더링하고 쿼리 API를 제공함 |
| `renderHook()` | 커스텀 훅을 테스트하기 위한 도구              |
| `cleanup()`    | DOM 정리 (자동 호출되지만 수동 호출도 가능)   |
| `act()`        | React의 상태 업데이트 등을 안전하게 실행      |
| `configure()`  | 전역 설정 (예: StrictMode 활성화)             |

## 참고 자료

[제련소](https://inblog.ai/myplace/userevent-keyboard-4942)
