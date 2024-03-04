# src/components/SearchBar.tsx

```tsx
import TextField from './TextField';
import Categories from './Categories';

type SearchBarProps = {
  categories: string[];
  filterText: string;
  setFilterText: (text: string) => void;
  setFilterCategory: (text: string) => void;
}

export default function SearchBar({
  categories, filterText, setFilterText, setFilterCategory,
}: SearchBarProps) {
  return (
    <div>
      <TextField
        label="검색"
        placeholder="식당 이름"
        text={filterText}
        setText={setFilterText}
      />
      <Categories
        categories={categories}
        setFilterCategory={setFilterCategory}
      />
    </div>
  );
}
```

## SearchBar.test.tsx

`SearchBar`컴포넌트는 `TextField`와 `Categories` 두 컴포넌트를 분리하는 역할\
두 컴포넌트 각각 렌더링 해야할 텍스트를 테스트

```tsx
import { render, screen } from '@testing-library/react';

import SearchBar from './SearchBar';

// given
describe('SearchBar', () => {
  const categories = ['한식', '중식', '일식'];

  const setFilterText = jest.fn();
  const setFilterCategory = jest.fn();

  function renderSearchBar() {
    render((
      <SearchBar
        categories={categories}
        filterText=""
        setFilterText={setFilterText}
        setFilterCategory={setFilterCategory}
      />
    ));
  }

  // when
  it('renders search label text', () => {
    renderSearchBar();

    // then
    screen.getByLabelText(/검색/);
  });

  // when
  it('renders all categories', () => {
    renderSearchBar();

    // then
    screen.getByText(/전체/);

    categories.forEach((category) => {
      screen.getByText(category);
    });
  });
});
```

### Given

props로 받는 `restaurants`에 식당 데이터 초기화

### 테스트 케이스 01

SearchBar renders search label text

#### when 01

- `SearchBar` 컴포넌트 렌더링

#### then 01

- 화면에 `TextField` 컴포넌트에서 렌더링 하는 '검색' 텍스트가 나오는지 확인

### 테스트 케이스 02

SearchBar renders all categories

#### when 02

- `SearchBar` 컴포넌트 렌더링

#### then 02

- 화면에 `Categories` 컴포넌트에서 렌더링 하는 '전체' 텍스트가 나오는지 확인
