# src/components/Category.tsx

특정 카테고리 버튼이 맞는지, 버튼을 클릭했을 때 특정 카테고리를 인자로 받는지 테스트

```tsx
type CategoryProps = {
  category: string;
  setFilterCategory: (text: string) => void;
}

export default function Category({
  category, setFilterCategory,
}: CategoryProps) {
  return (
    <li
      style={{
        marginRight: '1rem',
      }}
    >
      <button
        type="button"
        onClick={() => setFilterCategory(category)}
      >
        {category}
      </button>
    </li>
  );
}
```

## Category.test.tsx

```tsx
import { render, screen, fireEvent } from '@testing-library/react';

import Category from './Category';

// given
describe('Category', () => {
  const category = '한식';

  const setFilterCategory = jest.fn();

  function renderCategory() {
    render((
      <Category
        category={category}
        setFilterCategory={setFilterCategory}
      />
    ));
  }

  beforeEach(() => {
    jest.clearAllMocks();
  });

  // when
  it('renders category text', () => {
    renderCategory();

    // then
    screen.getByText('한식');
  });

  // when
  it('listens for category click event', () => {
    renderCategory();

    fireEvent.click(screen.getByText('한식'));

    // then
    expect(setFilterCategory).toBeCalledWith(category);
  });
});
```

### Given

- `category`가 '전체, 한식, 중식, 일식' 중 '한식'이였다고 한정

- 버튼을 클릭하면 호출되는 `setFilterCategory`을 mocking

- Category를 렌더링하는 로직을 `renderCategory`을 통해 실행하도록 빼줌

- 각 테스트 케이스 전 mock 함수 초기화

### 테스트 케이스 01

Category renders category text

#### when 01

- `Category` 컴포넌트 렌더링

#### then 01

- 화면에 '한식' 텍스트가 있는지 확인

### 테스트 케이스 02

Category listens for category click event

#### when 02

- `Category` 컴포넌트 렌더링

- '한식' 텍스트(버튼) 클릭

#### then 02

- `setFilterCategory` mock 함수가 `category`를 인자로 호출되는지 확인(`setFilterCategory(category)`)
