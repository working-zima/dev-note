# src/components/ReceiptPrinter.tsx

props로 오는 `receipt`가 비어있을 경우와 비어있지 않을 경우에 따라 화면에 잘 나오는지 테스트

```tsx
import _ from 'lodash';

import MenuItem from './MenuItem';

import Receipt from '../types/Receipt';

type ReceiptPrinterProps = {
  receipt: Receipt;
}

export default function ReceiptPrinter({ receipt }: ReceiptPrinterProps) {
  if (_.isEmpty(receipt)) {
    return (
      <p>[영수증 나오는 곳]</p>
    );
  }

  const { id, menu, totalPrice } = receipt;

  return (
    <div style={{
      width: '50%',
      border: '1px solid black',
      textAlign: 'center',
    }}
    >
      <h2>영수증</h2>
      <div>
        <h3>주문번호</h3>
        <p>{id}</p>
      </div>
      <div>
        <h3>주문목록</h3>
        <ul style={{
          padding: 0,
          listStyle: 'none',
        }}
        >
          {menu.map((food, index) => {
            const key = `${food.id}-${index}`;

            return (
              <MenuItem
                key={key}
                food={food}
              />
            );
          })}
        </ul>
      </div>
      <p>
        총 가격:
        {' '}
        {totalPrice.toLocaleString()}
        원
      </p>
    </div>
  );
}
```

## ReceiptPrinter.test.tsx

DCI 패턴을 사용하여 영수증을 출력하는 경우와 출력하지 않는 경우로 테스트 케이스를 나눔

```tsx
import { render, screen } from '@testing-library/react';

import Receipt from '../types/Receipt';

import ReceiptPrinter from './ReceiptPrinter';

import fixtures from '../../fixtures';

const context = describe;

describe('ReceiptPrinter', () => {
  context('with receipt', () => {
    // given
    const { receipt } = fixtures;

      // when
    it('renders receipt', () => {
      render(<ReceiptPrinter receipt={receipt} />);

      // then
      screen.getByText(/주문번호/);
      screen.getByText(/주문목록/);
      screen.getByText(/짜장면/);
    });
  });

  context('without receipt', () => {
    // given
    const receipt = {} as Receipt;

    // when
    it('renders message', () => {
      render(<ReceiptPrinter receipt={receipt} />);

      // then
      screen.getByText(/영수증 나오는 곳/);
    });
  });
});
```

### 테스트 케이스 01

ReceiptPrinter with receipt renders receipt

### Given 01

- `receipt`는 영수증 데이터가 담긴 객체

#### when 01

- `ReceiptPrinter`컴포넌트 렌더링

#### then 01

- 화면에 '주문번호, 주문목록, 짜장면' 텍스트가 있는지 확인

### 테스트 케이스 02

ReceiptPrinter without receipt renders message

### Given 02

- `receipt`는 영수증 데이터의 타입을 가진 빈 객체

#### when 02

- `ReceiptPrinter`컴포넌트 렌더링

#### then 02

- 화면에 '영수증 나오는 곳' 텍스트가 있는지 확인
