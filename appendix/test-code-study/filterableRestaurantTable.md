# src/FilterableRestaurantTable.tsx

화면에 텍스트가 잘 나오는지 테스트

```tsx
import { useState } from 'react';

import Restaurant from '../types/Restaurant';

import extractCategories from '../utils/extractCategories';
import filterRestaurants from '../utils/filterRestaurants';

import SearchBar from './SearchBar';
import RestaurantTable from './RestaurantTable';

type FilterableRestaurantTableProps = {
  restaurants: Restaurant[];
};

export default function FilterableRestaurantTable({
  restaurants,
}: FilterableRestaurantTableProps) {
  const [filterText, setFilterText] = useState<string>('');
  const [filterCategory, setFilterCategory] = useState<string>('전체');

  const categories = extractCategories(restaurants);

  const filteredRestaurants = filterRestaurants(restaurants, {
    filterText, filterCategory,
  });

  return (
    <div>
      <SearchBar
        categories={categories}
        filterText={filterText}
        setFilterText={setFilterText}
        setFilterCategory={setFilterCategory}
      />
      <RestaurantTable restaurants={filteredRestaurants} />
    </div>
  );
}
```

## FilterableRestaurantTable.test.tsx

```tsx
import { render, screen } from '@testing-library/react';

import FilterableRestaurantTable from './FilterableRestaurantTable';

import fixtures from '../../fixtures';

// given
describe('FilterableRestaurantTable', () => {
  const { restaurants } = fixtures;

  // when
  it('renders restaurants', () => {
    render(<FilterableRestaurantTable restaurants={restaurants} />);

    // then
    screen.getByText(/짜장면/);
    screen.getByText(/짬뽕/);
  });
});
```

### Given

- 식당들에 대한 정보를 가진 배열을 변수 `restaurants`을 가져옴

### 테스트 케이스 01

FilterableRestaurantTable renders restaurants

#### when 01

- `FilterableRestaurantTable` 컴포넌트 렌더링

#### then 01

- 식당의 메뉴인 '짜장면', '짬뽕' 텍스트가 화면에 있는지 확인
