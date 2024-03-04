# src/components/RestaurantTable.tsx

```tsx
import RestaurantRow from './RestaurantRow';

import Restaurant from '../types/Restaurant';

type RestaurantTableProps = {
  restaurants: Restaurant[];
};

export default function RestaurantTable({ restaurants }: RestaurantTableProps) {
  return (
    <div>
      <table>
        <thead>
          <tr>
            <th style={{ paddingInline: '2rem' }}>
              식당 이름
            </th>
            <th>
              종류
            </th>
            <th>
              메뉴
            </th>
          </tr>
        </thead>
        <tbody>
          {restaurants.map((restaurant) => (
            <RestaurantRow
              key={restaurant.id}
              restaurant={restaurant}
            />
          ))}
        </tbody>
      </table>
    </div>
  );
}
```

## RestaurantTable.test.tsx

하위 컴포넌트에서 렌더링할 데이터 보다 `RestaurantTable`컴포넌트에서만 렌더링 되는 '식당 이름, 종류, 메뉴' 테스트

```tsx
import { render, screen } from '@testing-library/react';

import RestaurantTable from './RestaurantTable';

import fixtures from '../../fixtures';

// given
describe('RestaurantTable', () => {
  const { restaurants } = fixtures;

  // when
  it('renders table headers', () => {
    render(<RestaurantTable restaurants={restaurants} />);

    // then
    screen.getByText('식당 이름');
    screen.getByText('종류');
    screen.getByText('메뉴');
  });
});

```

### Given

- props로 받는 `restaurants`에 식당 데이터 초기화

### 테스트 케이스 01

RestaurantTable renders table headers

#### when 01

- `RestaurantTable` 컴포넌트 렌더링

#### then 01

- 화면에 '식당 이름, 종류, 메뉴'이 나오는지 확인
