# src/components/MenuItem.tsx

메뉴와 메뉴를 선택할 수 있는 버튼으로 이루어져 있는 컴포넌트입니다.

```tsx
import { HTMLAttributes } from 'react';

import Food from '../types/Food';

type MenuItemProps = {
  food: Food;
} & HTMLAttributes<Element>;

export default function MenuItem({ food, children }: MenuItemProps) {
  const { name, price } = food;

  return (
    <li
      style={{
        display: 'flex',
        paddingBlock: '.5rem',
      }}
    >
      <span style={{ margin: '0 auto' }}>
        {name}
        (
        {price.toLocaleString()}
        원)
      </span>
      {children}
    </li>
  );
}
```

## MenuItem.test.tsx

```tsx
import { render, screen } from '@testing-library/react';

import MenuItem from './MenuItem';

// given
describe('MenuItem', () => {
  const food = {
    id: 'FOOD_ID',
    name: '짜장면',
    price: 8_000,
  };

  // when
  it('renders food information', () => {
    render(<MenuItem food={food} />);

    // then
    screen.getByText('짜장면(8,000원)');
  });

  // when
  it('renders children', () => {
    render((
      <MenuItem food={food}>
        <p>맛있어요!</p>
      </MenuItem>
    ));

    // then
    screen.getByText(/맛있어요!/);
  });
});
```

### Given

- `MenuItem` 컴포넌트의 props인 `food`에 음식 데이터 객체 초기화

### 테스트 케이스 01

MenuItem renders food information

#### when 01

- `MenuItem` 컴포넌트를 `food` props와 함께 랜더링

#### then 01

- props로 넘어온 `food` 데이터의 텍스트가 화면에 있는지 확인

### 테스트 케이스 02

MenuItem renders children

#### when 02

- `MenuItem`컴포넌트 태그 사이에 children 요소를 추가하여 랜더링

#### then 02

- children 요소가 잘 출력되는지 확인
