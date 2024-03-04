# src/components/RestaurantRow.tsx

식당 목록을 화면에 보여주는 컴포넌트

```tsx
import Restaurant from '../types/Restaurant';

import Menu from './Menu';

type RestaurantProps = {
  restaurant: Restaurant;
};

export default function RestaurantRow({ restaurant }: RestaurantProps) {
  const { name, category, menu } = restaurant;

  return (
    <tr>
      <td>
        {name}
      </td>
      <td>
        {category}
      </td>
      <td>
        <Menu menu={menu} />
      </td>
    </tr>
  );
}
```

## RestaurantRow.test.tsx

`RestaurantRow`컴포넌트는 `tr`태그로 이루어져 있음\
`tr` 태그는 `table` 태그 안에서 사용되어야 하기 때문에 테스트 코드에서 `RestaurantRow`컴포넌트를 렌더링 할 때 table, tbody 태그를 사용

```tsx
import { render, screen } from '@testing-library/react';

import RestaurantRow from './RestaurantRow';

import fixtures from '../../fixtures';

// given
describe('RestaurantRow', () => {
  const restaurant = {
    id: 'RESTAURANT_01',
    category: '중식',
    name: '메가반점',
    menu: [...fixtures.foods],
  };

  // when
  it('renders restaurant information', () => {
    render((
      <table>
        <tbody>
          <RestaurantRow restaurant={restaurant} />
        </tbody>
      </table>
    ));

    // then
    screen.getByText(/메가반점/);
    screen.getByText(/중식/);
    screen.getByText(/짜장면/);
  });
});
```

### Given

- props로 받는 `restaurant`에 식당 데이터 초기화

### 테스트 케이스 01

RestaurantRow renders restaurant information

#### when 01

- `RestaurantRow` 컴포넌트 렌더링

#### then 01

- 화면에 '메가반점, 중식, 짜장면'이 나오는지 확인
