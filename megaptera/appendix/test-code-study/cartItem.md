# src/components/CartItem.tsx

`CartItem`컴포넌트는 장바구니에 음식이 보이는지, 취소 버튼을 클릭했을 때 해당 음식의 취소버튼으로 인식되는지 테스트

```tsx
import MenuItem from './MenuItem';

import Food from '../types/Food';

type CartItemProps = {
  food: Food;
  index: number;
  onClickCancel: (index: number) => void;
}

export default function CartItem({
  food, index, onClickCancel,
}: CartItemProps) {
  return (
    <MenuItem food={food}>
      <button
        style={{ marginLeft: '.5rem' }}
        name={`#${food.name}`}
        type="button"
        onClick={() => onClickCancel(index)}
      >
        취소
      </button>
    </MenuItem>
  );
}
```

## CartItem.test.tsx

```tsx
import { fireEvent, render, screen } from '@testing-library/react';

import CartItem from './CartItem';

// given
describe('CartItem', () => {
  const food = {
    id: 'FOOD_ID',
    name: '짜장면',
    price: 8_000,
  };
  const index = 1;

  const handleClickCancel = jest.fn();

  beforeEach(() => {
    jest.clearAllMocks();
  });

  function renderCartItem() {
    render((
      <CartItem
        food={food}
        index={index}
        onClickCancel={handleClickCancel}
      />
    ));
  }

  // when
  it('renders item information', () => {
    renderCartItem();

    // then
    screen.getByText('짜장면(8,000원)');
  });

  // when
  it('listens for cancel button click event', () => {
    renderCartItem();

    fireEvent.click(screen.getByText('취소'));

    // then
    expect(handleClickCancel).toBeCalledWith(index);
  });
});
```

### Given

- food` 변수에 `CartItem`에 보낼 객체를 초기화

- `food`에 음식이 하나이기 때문에 `index`에 1을 초기화

- `handleClickCancel`은 테스트에 사용하지 않을 것이기 때문에 mocking

- 각 테스트 케이스 실행 전에 mock함수 초기화

- props가 많기 때문에 `renderCartItem`함수로 분리하여 `CartItem` 컴포넌트 실행

### 테스트 케이스 01

CartItem renders item information

#### when 01

- `CartItem`컴포넌트를 실행

#### then 01

- 화면에 `food`의 `name`이 있는지 확인

### 테스트 케이스 02

CartItem listens for cancel button click event

#### when 02

- `CartItem`컴포넌트를 실행

- 화면에 취소 버튼을 클릭

#### then 02

- `handleClickCancel`함수가 설정한 `index`로 호출 되었는지 검증

- `handleClickCancel`함수는 `handleClickCancel(index)`로 호출됨
