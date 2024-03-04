# src/components/Cart.tsx

`Cart`컴포넌트는 '주문 바구니'라는 것과 바구니에 담긴 음식을 잘 보여주고 있는지 테스트

```tsx
import { useLocalStorage } from 'usehooks-ts';

import useCreateOrder from '../hooks/useCreateOrder';

import Receipt from '../types/Receipt';
import Food from '../types/Food';

import CartItem from './CartItem';
import OrderButton from './OrderButton';

type CartProps = {
  setReceipt: (receipt: Receipt) => void;
}

export default function Cart({ setReceipt }: CartProps) {
  const [selectedFoods, setFoods] = useLocalStorage<Food[]>('cart', []);

  const { createOrder } = useCreateOrder();

  const handleClickCancel = (index: number) => {
    const foods = selectedFoods.filter((_, i) => i !== index);
    setFoods(foods);
  };

  const handleClickOrder = async () => {
    if (!selectedFoods.length) {
      return;
    }

    const receipt = await createOrder(selectedFoods);
    setReceipt(receipt);

    setFoods([]);
  };

  return (
    <div style={{ marginBottom: '3rem' }}>
      <h2>
        주문 바구니
      </h2>
      <ul style={{ width: '20%' }}>
        {selectedFoods.map((food, index) => {
          const key = `${food.id}-${index}`;

          return (
            <CartItem
              key={key}
              index={index}
              food={food}
              onClickCancel={handleClickCancel}
            />
          );
        })}
      </ul>
      <OrderButton
        foods={selectedFoods}
        onClick={handleClickOrder}
      />
    </div>
  );
}
```

## Cart.test.tsx

```tsx
import { render, screen } from '@testing-library/react';

import Cart from './Cart';

import fixtures from '../../fixtures';

// Given
const setFoods = jest.fn();

jest.mock('usehooks-ts', () => ({
  useLocalStorage: () => [
    fixtures.foods,
    setFoods,
  ],
}));

describe('Cart', () => {
  const setReceipt = jest.fn();

  beforeEach(() => {
    jest.clearAllMocks();
  });

  // When
  it('renders title', () => {
    render(<Cart setReceipt={setReceipt} />);

    // Then
    screen.getByText(/주문 바구니/);
  });

  // When
  it('renders order food list', () => {
    render(<Cart setReceipt={setReceipt} />);

    // Then
    screen.getByText(/짜장면/);
    screen.getByText(/짬뽕/);
  });
});
```

### Given

- `setFoods`라는 단일 함수를 mocking

- `jest.mock( 가상으로 대체하려는 모듈의 경로, 모듈을 대체할 가상 모듈의 구현 함수 )`을 사용\
`usehooks-ts` 모듈에서 넘어오는 모든 함수를 mock 함수로 전환\
테스트를 돌릴 때 `useLocalStorage()`를 사용할 경우 반환값은 `[fixtures.foods, setFoods]`

- `setReceipt`라는 단일 함수를 mocking

- `clearAllMocks`로 각 테스트 케이스 전에 mock 함수들 초기화

### 테스트 케이스 01

Cart renders title

#### when 01

- `Cart`컴포넌트를 가져오고 `setReceipt` 모킹 함수를 props로 받아옮

#### then 01

- 화면에 '주문 바구니' 텍스트가 있는지 확인

### 테스트 케이스 02

Cart renders order food list

#### when 02

- `Cart`컴포넌트를 가져오고 `setReceipt` 모킹 함수를 props로 받아옮

#### then 02

- 화면에 '짜장면'과 '짬뽕' 텍스트가 있는지 확인
