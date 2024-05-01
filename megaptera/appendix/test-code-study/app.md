# src/App.tsx

```tsx
import { useEffect } from 'react';

import { useBoolean, useInterval, useLocalStorage } from 'usehooks-ts';

import Receipt from './types/Receipt';

import Cart from './components/Cart';
import FilterableRestaurantTable from './components/FilterableRestaurantTable';
import ReceiptPrinter from './components/ReceiptPrinter';

import useFetchRestaurants from './hooks/useFetchRestaurants';

const emptyReceipt = {} as Receipt;

export default function App() {
  const { value, setTrue, setFalse } = useBoolean(false);

  const [receipt, setReceipt] = useLocalStorage('receipt', emptyReceipt);

  const restaurants = useFetchRestaurants();

  useEffect(() => {
    setFalse();

    if (receipt.id) {
      setTrue();
    }
  }, [receipt]);

  useInterval(() => {
    if (receipt.id) {
      setReceipt(emptyReceipt);
      setFalse();
    }
  }, value ? 5000 : null);

  return (
    <div>
      <h1>푸드코트 키오스크</h1>
      <Cart setReceipt={setReceipt} />
      <FilterableRestaurantTable restaurants={restaurants} />
      <ReceiptPrinter receipt={receipt} />
    </div>
  );
}
```

## App.test.tsx

```tsx
import { render, waitFor, screen } from '@testing-library/react';

import App from './App';

describe('App', () => {
  it('renders restaurants', async () => {
    render(<App />);

    await waitFor(() => {
      screen.getByText(/메가반점/);
      screen.getByText(/메리김밥/);
    });
  });
});
```

`App`컴포넌트가 식당 목록을 브라우저에 그려지는지에 대한 테스트입니다.
`waitFor()`을 사용하여 '메가반점'과 '메리김밥' 텍스트가 가져와질 때까지 확인합니다.
