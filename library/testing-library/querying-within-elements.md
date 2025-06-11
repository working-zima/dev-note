# Querying Within Elements

`within`(또는 `getQueriesForElement`의 별칭)은 DOM 요소를 인자로 받아 해당 요소에 쿼리 함수를 바인딩합니다.\
이렇게 하면 컨테이너를 명시하지 않고도 쿼리 함수를 사용할 수 있습니다.\
이 방식은 이 API를 기반으로 하는 라이브러리에서 권장되는 접근 방식이며, React Testing Library와 Vue Testing Library 내부에서도 사용됩니다.

즉, `within()` 함수는 특정 DOM 요소 안에서만 텍스트나 버튼 같은 요소를 찾고 싶을 때 사용하는 도구입니다.

## within 예시

### 'messages'라는 섹션 안에서만 'hello'라는 텍스트를 찾고 싶을 경우

```jsx
render(
  <section aria-label="messages">
    <h2>messages</h2>
    <p>hello</p>
  </section>

  <section aria-label="notifications">
    <h2>notifications</h2>
    <span>hello</span>
  </section>
);
```

```tsx
import { render, within } from "@testing-library/react";

const { getByText } = render(<MyComponent />);

// 1. "messages"라는 label이 있는 요소를 찾고
const messagesSection = screen.getByLabelText("messages");

// 2. 그 요소 안에서만 "hello"를 찾음
const helloMessage = within(messagesSection).getByText("hello");
```

### within()을 변수에 저장해서 재사용

`within()`은 쿼리 함수들을 담고 있는 객체를 반환합니다.\
따라서 여러 개의 하위 요소를 검사할 때는 다음처럼 변수로 꺼내서 재사용할 수 있습니다.

```tsx
const cards = screen.getAllByTestId("product-card");

cards.forEach((cardEl, index) => {
  const utils = within(cardEl); // card 요소 전용 쿼리 도구

  expect(utils.getByText(data.products[index].title)).toBeInTheDocument();
  expect(
    utils.getByText(data.products[index].category.name)
  ).toBeInTheDocument();
});
```
