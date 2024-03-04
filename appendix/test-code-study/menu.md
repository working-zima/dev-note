# src/components/Menu.tsx

props인 `menu`에 데이터가 있는지 없는지에 따라 다른 결과를 테스트

```tsx
import { useLocalStorage } from 'usehooks-ts';

import Food from '../types/Food';

import MenuItem from './MenuItem';

type MenuProps = {
  menu: Food[];
};

export default function Menu({ menu }: MenuProps) {
  const [selectedFoods, selectFood] = useLocalStorage<Food[]>('cart', []);

  if (!menu.length) {
    return (
      <p>메뉴가 존재하지 않습니다</p>
    );
  }

  const handleClickSelect = (food: Food) => {
    selectFood([
      ...selectedFoods,
      food,
    ]);
  };

  return (
    <ul>
      {menu.map((food, index) => {
        const key = `${food.id}-${index}`;

        return (
          <MenuItem
            key={key}
            food={food}
          >
            <button
              style={{ marginLeft: '.5rem' }}
              name={`#${food.name}`}
              type="button"
              onClick={() => handleClickSelect(food)}
            >
              선택
            </button>
          </MenuItem>
        );
      })}
    </ul>
  );
}
```

## Menu.test.tsx

DCI 형식을 사용하여 `Menu`의 props인 `menu`가 빈 배열이냐, 아니냐에 따라 다른 상황을 테스트

```tsx
import { render, screen } from '@testing-library/react';

import Food from '../types/Food';

import Menu from './Menu';

import fixtures from '../../fixtures';

const context = describe;

describe('Menu', () => {
  // given
  context('with menu', () => {
    const menu = fixtures.foods;

    // when
    it('renders foods list', () => {
      render(<Menu menu={menu} />);

      // then
      screen.getByText(/짜장면/);
      screen.getByText(/짬뽕/);
    });
  });

  // given
  context('without menu', () => {
    const menu: Food[] = [];

    // when
    it('renders no foods message', () => {
      render(<Menu menu={menu} />);

      // then
      screen.getByText(/메뉴가 존재하지 않습니다/);
    });
  });
});

```

### 테스트 케이스 01

Menu with menu renders foods list

#### Given 01

- `menu`에 메뉴 데이터를 초기화

#### when 01

- `Menu` 컴포넌트를 렌더링

#### then 01

- 메뉴 데이터인 '짜장면, 짬뽕'이 텍스트가 화면에 있는지 확인

### 테스트 케이스 02

Menu without menu renders no foods message

#### Given 02

- `menu`에 빈 배열을 초기화

#### when 02

- `Menu` 컴포넌트를 렌더링

#### then 02

- '메뉴가 존재하지 않습니다' 텍스트가 화면에 있는지 확인
