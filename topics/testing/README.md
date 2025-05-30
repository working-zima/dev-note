# Testing

## 테스트가 당신의 코드에 미치는 영향

실무에 바로 적용하는 프론트엔드 테스트  
발표자: 이재성

### 테스트란?

- 애플리케이션의 품질과 안정성을 높이기 위해 사전에 결함을 찾아내고 수정하기 위한 행위
- 주로 특정 모듈(특히 컴포넌트)이 사양에 맞게 동작하는지 자동화된 테스트로 검증
- 하지만 그만큼 개발자의 개발 비용이 증가함 📈

## 테스트 코드의 효과

### 1. 좋은 설계에 대한 사고를 도와준다

- 테스트를 작성하면 결합도가 낮은 구조로 자연스럽게 유도됨
- 모듈 간 의존성이 줄어들어 유지보수가 쉬워짐

#### 결합도(coupling)

- 어떤 모듈이 다른 모듈에 의존하는 정도
- 결합도가 높을수록: 변경 시 다른 모듈에도 영향 발생
- 결합도가 낮을수록: 각 모듈을 독립적으로 테스트하고 변경 가능

#### 결합도로 인한 테스트 복잡도 향상

- 특정 기능만 따로 테스트하기 어려움
- 구조가 복잡해질수록 테스트 누락 가능성 증가
- 테스트 코드 수정도 빈번하게 발생

#### 개선된 설계 예시

```tsx
<Navigation />
<SearchBar />
<Category />
<PriceRange />

<Product />
<Product />
...
```

→ 각 컴포넌트를 분리하면 독립적 테스트 가능

### 2. 테스트 코드를 기반으로 빠르고 안정적으로 리팩토링할 수 있다

#### 리팩토링이란?

- 결과의 변경 없이 코드의 구조를 재조정하는 작업

#### 문제점

- 거대한 모듈을 한꺼번에 리팩토링하면 문제가 생겼을 때 원인 파악이 어려움

#### 해결책

- 작은 단위로 나눠 테스트와 함께 개선하면 문제 발생 시 빠르게 위치 파악 가능

> “한 가지를 수정할 때마다 테스트하면,  
> 오류가 생기더라도 변경 폭이 작기 때문에  
> 살펴볼 범위도 줄어서 문제를 찾고 해결하기가 훨씬 쉽다.  
> 이렇게 조금씩 변경하고 매번 테스트 하는 것은  
> 리팩터링 절차의 핵심이다.”  
> — 마틴 파울러, 『리팩터링』

### 3. 좋은 테스트 코드는 애플리케이션의 이해를 돕는 문서가 된다

#### 테스트 코드 예시 (Cypress)

```ts
describe("메인 홈", () => {
  it("초기 상품은 20개가 노출된다.", () => {
    cy.findAllByTestId("product-card").should("have.length", 20);
  });

  it("show more 버튼 클릭 시 20개 더 노출된다.", () => {
    cy.findByText("Show more").click();
    cy.findAllByTestId("product-card").should("have.length", 40);
  });

  it("상품 클릭 시 상세 페이지로 이동한다.", () => {
    cy.findAllByTestId("product-card").eq(0).click();
    cy.assertUrl("/product/6");
  });
});
```

### 정리

- 테스트는 애플리케이션의 품질과 안정성을 확보하기 위한 자동화된 수단이다.
- 좋은 테스트 코드는 설계를 개선하고, 리팩토링을 안정화하며, 문서화 역할도 한다.

## 올바른 테스트 작성을 위한 규칙

### 1. 인터페이스를 기준으로 테스트를 작성하자

- 내부 구현을 검증하는 테스트는 캡슐화를 위반하고 깨지기 쉬움
- 테스트 유지보수가 어려워지고, 테스트 코드가 복잡해질 수 있음

#### ❌ 잘못된 테스트 예시

```ts
it('isShowModal 상태를 true로 변경했을 때 ModalComponent의 display 스타일이 block이며, "안녕하세요!" 텍스트가 노출된다', () => {
  SpecificComponent.setState({ isShowModal: true });
});
```

- 상태 변경을 테스트 코드에서 직접 수행
- 내부 구현에 종속적이며 유지보수가 어렵고, 무엇을 검증하는지 한 눈에 파악하기 어려움

#### ✅ 좋은 테스트 예시

```ts
it("버튼을 누르면 모달을 띄운다.", () => {
  user.click(screen.getByRole("button"));
});
```

- 사용자 인터페이스 기준의 테스트
- 캡슐화 위반 없이 행위를 기준으로 명확하게 테스트함

### 2. 커버리지보다는 의미 있는 테스트인가?

#### 테스트 커버리지란?

- 테스트가 전체 코드에서 얼마나 실행되었는지를 나타내는 지표  
  (Statements, Branches, Functions, Lines)

#### 한계

- 커버리지만 높고 검증이 없는 테스트는 무의미함

```ts
function isLargerThan5(value) {
  if (typeof value !== "number") return false;
  return value > 5;
}

it("isLargerThan5 test", () => {
  const result = isLargerThan5(100);
  const result2 = isLargerThan5("hello");
});
```

- 실행은 되었지만 결과 검증이 없음

### 3. 테스트가 필요 없는 코드도 있다

- 타입 시스템 또는 명확한 로직으로 보장되는 유틸 함수

```ts
export const isNumber = (value) => typeof value === "number";
export const isString = (value) => typeof value === "string";
```

### 4. 테스트 코드도 유지 보수 대상이다

#### 테스트 명확하게 작성하자

```ts
// ❌
it("리스트에서 항목이 삭제된다", () => {...});

// ✅
it("항목들을 체크한 후 삭제 버튼을 누르면 리스트에서 체크된 항목들이 삭제된다", () => {...});
```

#### 하나의 테스트에는 하나의 동작만

```ts
// ❌
it("상품 노출/수량 변경/삭제 테스트", () => {...});

// ✅
it("상품들이 정상적으로 노출된다", () => {...});
it("수량을 변경하면 가격이 재계산된다", () => {...});
it("삭제 버튼을 누르면 상품이 삭제된다", () => {...});
```

### 핵심 정리

- 인터페이스 중심 테스트 작성
- 내부 구현은 테스트하지 않는다 — 유지보수성과 캡슐화를 위함
- 의미 있는 테스트를 고민한다 — 커버리지보다 행동 보장이 중요
- SRP(Single Responsibility Principle)에 따라 명확하고 단일 동작 테스트를 작성한다

## 단위 테스트란 무엇인가?

### 정의

- 단일 함수 또는 단일 컴포넌트(클래스)의 결과값, 상태, 행위를 검증하는 테스트

### 단위 테스트 대상

- 버튼, 인풋, 캐러셀 등 공통 컴포넌트
- 다른 컴포넌트와의 상호작용이 없고 비즈니스 로직이 명확하게 분리된 컴포넌트

### Arrange - Act - Assert 패턴

1. **Arrange**: 테스트를 위한 환경 구성  
   (예: `render()`로 컴포넌트 렌더링)
2. **Act**: 테스트 대상 동작 발생  
   (예: 클릭, 입력 등 사용자 이벤트)
3. **Assert**: 기대 결과가 발생했는지 검증  
   (예: 특정 텍스트 또는 요소가 DOM에 존재하는지 확인)

### React Testing Library 쿼리 타입

| 쿼리 타입     | 0개 매치     | 1개 매치       | 1개 이상 매치  | 재시도 (Async/Await) |
| ------------- | ------------ | -------------- | -------------- | -------------------- |
| getBy...      | 에러 발생    | 요소 반환      | 에러 발생      | ✗                    |
| queryBy...    | null 반환    | 요소 반환      | 에러 발생      | ✗                    |
| findBy...     | 에러 발생    | 요소 반환      | 에러 발생      | ○                    |
| getAllBy...   | 에러 발생    | 요소 배열 반환 | 요소 배열 반환 | ✗                    |
| queryAllBy... | 빈 배열 반환 | 요소 배열 반환 | 요소 배열 반환 | ✗                    |
| findAllBy...  | 에러 발생    | 요소 배열 반환 | 요소 배열 반환 | ○                    |

### React Testing Library 쿼리 종류

| 종류              | 설명                                            |
| ----------------- | ----------------------------------------------- |
| ByRole            | role 속성을 기준으로 요소 선택                  |
| ByLabelText       | label을 기준으로 input 요소 선택                |
| ByPlaceholderText | placeholder 속성 기반으로 선택                  |
| ByText            | 텍스트 내용 기반으로 요소 선택                  |
| ByDisplayValue    | 현재 표시된 값 기준으로 input, select 요소 선택 |
| ByAltText         | alt 속성을 가진 요소 선택 (img 등)              |
| ByTitle           | title 속성을 가진 요소 선택 (svg 등)            |
| ByTestId          | data-testid 속성을 가진 요소 선택               |

## 테스트 환경과 매처

### 테스트 프레임워크란?

- 테스트 실행 및 검증을 위한 환경  
  (테스트 러너, 어설션, 플러그인 등 포함)

| 항목                     | 제공 도구                 | 대표 메서드 예시                             | 설명                            |
| ------------------------ | ------------------------- | -------------------------------------------- | ------------------------------- |
| 테스트 실행 및 구조 정의 | Jest / Vitest             | `test()`, `describe()`, `expect()`           | 테스트 파일 실행 및 구조 정의   |
| DOM 테스트 유틸          | @testing-library/react    | `render()`, `screen.getByRole()`             | 실제 사용자 환경처럼 DOM 테스트 |
| Matcher 확장             | @testing-library/jest-dom | `toBeInTheDocument()`, `toHaveTextContent()` | DOM에 특화된 matcher 지원       |

### Vitest

- Vite 기반 테스트 프레임워크
- ESM, TS, JSX 지원
- Jest API와 호환됨

[Vitest 공식 가이드](https://vitest.dev/guide/features.html)  
[Vitest 설정 문서](https://vitest.dev/config/)

### 주요 개념 요약

#### `it` 함수

- 테스트 단위를 정의하며 `test()`의 alias
- `"should ~"` 형식의 설명이 어울림

#### `describe` 함수

- 관련된 테스트들을 그룹핑
- 테스트 컨텍스트를 명확히 분리하여 관리 가능

#### Assertion (단언)

- 예상되는 결과가 실제로 발생했는지 검증

#### Matcher (매처)

- `expect()`와 함께 사용되는 API
- 예: `toBe()`, `toEqual()`, `toBeInTheDocument()` 등

## Setup과 teardown

### 모든 테스트는 독립적으로 실행되어야 한다

순서만 바뀌었을 뿐인데 문제가 발생하면 좋은 테스트가 아닙니다.

#### 잘못된 테스트 예시

```ts
// ✅ 'A test' 이후 'B test' 통과
it('A test', () => { ... });
it('B test', () => { ... });

// ❌ 'B test' 이후 'A test' 는 통과하지 못함
it('B test', () => { ... });
it('A test', () => { ... });
```

### setup

테스트를 실행하기 전 수행해야 하는 작업

- `beforeAll`: 주로 스코프 내에서 공유할 환경, 상태를 설정할 때 유용

### teardown

테스트를 실행한 뒤 수행해야 하는 작업

- `afterEach`: 테스트에 의해 생성된 상태를 초기화 하는 경우에 유용

### setup과 teardown을 전역적으로 적용

`setupFiles`를 활용합니다.

#### vite.config.js

```js
import path from "path";

import react from "@vitejs/plugin-react";
import eslint from "vite-plugin-eslint";
import { defineConfig } from "vitest/config";

export default defineConfig({
  plugins: [react(), eslint({ exclude: ["/virtual:/**", "node_modules/**"] })],
  test: {
    globals: true, // 각 모듈에서 vitest를 import 하지 않고 사용 가능
    environment: "jsdom", // Node.js에 브라우저 DOM 환경이 없어서 js-dom  라이브러리로 가상 DOM 환경 사용
    setupFiles: "./src/utils/test/setupTests.js", // 활용
  },
  resolve: {
    alias: [{ find: "@", replacement: path.resolve(__dirname, "src") }],
  },
});
```

#### setupFiles.js

```js
import "@testing-library/jest-dom";

// 모킹한 모듈의 히스토리를 초기화
afterEach(() => {
  vi.clearAllMocks();
});

afterAll(() => {
  vi.resetAllMocks();
});

// https://github.com/vitest-dev/vitest/issues/821
Object.defineProperty(window, "matchMedia", {
  writable: true,
  value: vi.fn().mockImplementation((query) => ({
    matches: false,
    media: query,
    onchange: null,
    addListener: vi.fn(), // deprecated
    removeListener: vi.fn(), // deprecated
    addEventListener: vi.fn(),
    removeEventListener: vi.fn(),
    dispatchEvent: vi.fn(),
  })),
});
```

### Setup과 teardown 정리

- setup과 teardown를 통해 테스트 실행 전, 후 실행되어야 할 반복 작업을 깔끔하
  게 관리할 수 있습니다.

- 테스트 별로 별도의 스코프로 동작하기 때문에 독립적인 테스트를 구성하는데 도움을 받을 수 있습니다.

- setup, teardown에서 전역 변수를 사용한 조건 처리는 독립성을 보장하지 못하고, 신뢰성이 낮아지므로 지양해야 합니다.

## React Testing Library와 컴포넌트 테스트

### Testing Library

UI 컴포넌트 테스트를 도와주는 라이브러리입니다.

React, Cypress, Testcafe, Svelte, Vue, Angular, ReasonReact, Puppeteer, React Native, Preact, Nightwatch, ...

### Testing Library의 핵심 철학

Testing Library의 핵심 철학은 UI 컴포넌트를 사용자가 사용하는 방식으로 테스트 하는것 입니다.\
사용자가 사용하는 방식이란 RTL의 경우 DOM 노드를 쿼리(조회)하고 사용자와 비슷한 방식으로 이벤트를 발생시키는 것입니다.

### 인터페이스를 기준으로 테스트를 작성

#### 올바른 테스트 코드

```ts
it("버튼을 누르면 모달을 띄운다", () => {
  // 유저의 동작과 비슷하도록 클릭 이벤트를 발생
  user.click(screen.getByRole("button"));

  // ...
});
```

- 내부 구현과 종속성이 없으며 캡슐화에 위반되지 않음
- DOM, 이벤트 인터페이스 기반으로 검증
- 테스트 코드를 직관적으로 이해할 수 있음

### spy 함수

스트 코드에서 특정 함수가 호출되되었는지, 함수의 인자로 어떤 것이 넘어왔는지 어떤 값을 반환하는지 감시합니다.

#### spy 함수 예시

```jsx
// render.jsx
import { render } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

export default async (component) => {
  const user = userEvent.setup();

  return {
    user,
    ...render(component),
  };
};
```

```ts
import render from "@/utils/test/render";

it("텍스트를 입력하면 onChange prop으로 등록한 함수가 호출된다.", async () => {
  // 스파이 함수 생성
  const spy = vi.fn();

  // 스파이 함수로 onChange에 입력되는 이벤트 감시
  const { user } = await render(<TextField onChange={spy} />);

  const textInput = screen.getByPlaceholderText("텍스트를 입력해 주세요.");

  // onChange에 "test" 입력
  await user.type(textInput, "test");

  // spy에서 입력된 인자를 확인
  expect(spy).toHaveBeenCalledWith("test");
});

it("포커스가 활성화되면 onFocus prop으로 등록한 함수가 호출된다.", async () => {
  // 포커스 활성화
  // 1. 탭 키로 인풋 요소를 포커스 이동
  // 2. 인풋 요소를 클릭
  // 3. textInput.focus()로 직접 발생
  const spy = vi.fn();

  const { user } = await render(<TextField onFocus={spy} />);

  const textInput = screen.getByPlaceholderText("텍스트를 입력해 주세요.");

  // 인풋 요소를 클릭
  await user.click(textInput);

  // spy 함수가 호출되었는지 검증
  expect(spy).toHaveBeenCalled();
});
```

### React Testing Library와 컴포넌트 테스트 정리

React Testing Library는

- UI 컴포넌트를 사용자가 사용하는 방식으로 테스트

- 사용자가 앱을 사용하는 방식과 테스트 방식이 유사할수록 신뢰성은 향상

- DOM과 이벤트 인터페이스를 기반으로 요소를 조회하고, 다양한 동작을 시뮬레이션 할 수 있음

- 요소 조회를 위한 쿼리는 다양하며, 우선 순위가 존재

- 인터페이스 기반의 직관적인 코드와 내부 구현에 종속되지 않는 견고한 테스트 코드

Spy 함수는

- 함수의 호출 여부, 인자, 반환 값 등 함수 호출에 관련된 다양한 값을 저장

- 콜백 함수나 이벤트 핸들러가 올바르게 호출 되었는지 검증할 수 있음

- `toHaveBeenCalledWith`, `toHaveBeenCalled` 매처

## 단위 테스트 대상 선정하기

앱에서 테스트 가능한 가장 작은 소프트웨어(단일 함수 또는 단일 컴포넌트 또느 클래스)를 실행해 예상대로 동작하는지 확인하는 테스트 하여 결괏값 또는 상태(UI), 행위를 검증합니다.

### 단위 테스트 대상 선정하기 정리

쇼핑몰 예제 프로젝트의 단위 테스트 전략

- state나 로직처리 없이 UI만 그리는 컴포넌트는 검증하지 않는다.

  - 해당 검증은 스토리북과 같은 도구를 통해 검증

- 간단한 로직 처리만 하는 컴포넌트는 상위 컴포넌트의 통합 테스트에서 검증한다.

- 공통 유틸 함수는 단위 테스트로 검증한다.
  - 다른 모듈과의 의존성의 없다.
  - 여러 곳에서 사용되기 때문에 검증을 통해 안정성을 높인다.

## 모듈 모킹(Mocking)

실제 모듈·객체와 동일한 동작을 하도록 만든 모의 모듈·객체(Mock)로 실제를 대체하는 것입니다.

### 모킹의 장단점

- 외부 모듈과 의존성을 제외한 필요한 부분만 검증이 가능
- 실제 모듈과 완전히 동일한 모의 객체를 구현하는 것은 큰 비용
- 모의 객체를 남용하는 것은 테스트 신뢰성을 낮춤

### 모킹 초기화

- 다른 테스트에서는 특정 모듈에 대한 모킹이 필요없다면?
- 특정 테스트의 모킹 작업이 다른 테스트에 영향을 준다면?

테스트의 신뢰성을 떨어뜨릴 수 있습니다.\
따라서 테스트를 실행한 뒤에는 명시적으로 모킹 초기화를 해야 합니다.

### react-router-dom의 `useNavigate()`를 검증해야 할까?

`useNavigate()`에 대한 추가 검증은 불필요합니다.\
단위 테스트에서 외부 모듈에 대한 검증은 완전히 분리하고 모듈의 특정 기능을 제대로 호출하는지만 검증하면 됩니다.

### 모듈 모킹(Mocking) 정리

#### 모킹 정리

- 실제 모듈·객체와 동일한 동작을 하도록 만든 모의 모듈·객체(Mock)로 실제를 대체하는 것

- `vi.mock()`을 사용해 특정 모듈을 모킹할 수 있다.

- 외부 모듈의 검증은 완전히 배제하고, 대상이 되는 컴포넌트의 테스트만 독립적으로 작성할 수 있다.

- 단, 외부 모듈 역시 별도로 검증되어야 한다.

#### 모킹 초기화 정리

- 각 테스트의 독립성과 안정성을 보장하기 위해 teardown에서 모킹을 초기화 하자

- vitest의 resetAllMocks, clearAllMocks, restoreAllMocks를 활용해
