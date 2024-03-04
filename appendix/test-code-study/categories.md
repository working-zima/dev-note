# src/components/Categories.tsx

'전체, 한식, 중식, 일식' 버튼이 있는지 테스트

```tsx
import Category from './Category';

type CategoriesProps = {
  categories: string[];
  setFilterCategory: (text: string) => void;
}

export default function Categories({
  categories, setFilterCategory,
}: CategoriesProps) {
  return (
    <ul style={{
      display: 'flex',
      padding: 0,
      listStyle: 'none',
    }}
    >
      {['전체', ...categories].map((category: string) => (
        <Category
          key={category}
          category={category}
          setFilterCategory={setFilterCategory}
        />
      ))}
    </ul>
  );
}
```

## Categories.test.tsx

```tsx
import { render, screen } from '@testing-library/react';

import Categories from './Categories';

// given
describe('Categories', () => {
  const categories = ['한식, 중식, 일식'];

  const setFilterCategory = jest.fn();

  // when
  it('renders all categories', () => {
    render((
      <Categories
        categories={categories}
        setFilterCategory={setFilterCategory}
      />
    ));

    // then
    screen.getByText(/전체/);

    categories.forEach((category) => {
      screen.getByText(category);
    });
  });
});
```

### Given

- `Categories`의 props인 `categories` 초기화

- `Categories`의 props인 `setFilterCategory` mocking

### 테스트 케이스 01

Categories renders all categories

#### when 01

- `Cart`컴포넌트를 출력

#### then 01

- 화면에 '전체, 한식, 중식, 일식' 텍스트가 있는지 확인
