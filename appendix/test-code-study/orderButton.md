# src/components/OrderButton.tsx

주문 바구니에 담긴 음식들을 주문하는 버튼\
음식들의 합계가 잘 계산되는지, 버튼을 클릭하면 props로 받은 함수가 잘 호출되는지 테스트

```tsx
import Food from '../types/Food';

type OrderButtonProps = {
  foods: Food[];
  onClick: () => void;
}

export default function OrderButton({ foods, onClick }: OrderButtonProps) {
  const totalPrice = foods.reduce((acc, cur) => acc + cur.price, 0);

  return (
    <button
      type="button"
      onClick={onClick}
    >
      합계:
      {' '}
      {totalPrice.toLocaleString()}
      원 주문
    </button>
  );
}
```

## OrderButton.test.tsx

```tsx
import { render, screen, fireEvent } from '@testing-library/react';

import OrderButton from './OrderButton';

// given
describe('OrderButton', () => {
  const foods = [
    {
      id: 'FOOD_01',
      name: '짜장면',
      price: 8_000,
    },
    {
      id: 'FOOD_02',
      name: '짬뽕',
      price: 5_000,
    },
  ];

  const handleClickOrder = jest.fn();

  beforeEach(() => {
    jest.clearAllMocks();
  });

  function renderOrderButton() {
    render((
      <OrderButton
        foods={foods}
        onClick={handleClickOrder}
      />
    ));
  }

  // when
  it('renders order total price', () => {
    renderOrderButton();

    // then
    screen.getByText(/합계: 13,000원 주문/);
  });

  // when
  it('listens for order click event', () => {
    renderOrderButton();

    fireEvent.click(screen.getByText(/합계: 13,000원 주문/));

    // then
    expect(handleClickOrder).toBeCalled();
  });
});
```

### Given

- 주문 합계를 계산할 수 있도록 음식 데이터를 `foods`에 초기화

- 버튼을 클릭할 때 발생하는 `handleClickOrder`을 mocking

- 각 테스트 케이스 전 mock 함수 초기화

- `renderOrderButton`는 `OrderButton`컴포넌트를 렌더링 하는 함수

### 테스트 케이스 01

OrderButton renders order total price

#### when 01

- `OrderButton`컴포넌트를 렌더링

#### then 01

- 화면에 '합계: 13,000원 주문' 텍스트 확인

### 테스트 케이스 02

OrderButton listens for order click event

#### when 02

- `OrderButton`컴포넌트를 렌더링

- '합계: 13,000원 주문'텍스트 클릭(주문 버튼 클릭)

#### then 02

- `handleClickOrder` 함수가 호출되었는지 확인
