# 4. 장바구니 보기

## 장바구니 보기

장바구니에 상품 담기 기능을 만들기 전에, 비교적 쉬운 장바구니 보기 작업을 먼저 끝내보자.

```tsx
// src/pages/CartPage.tsx

export default function CartPage() {
  const { cart } = useFetchCart();

  if (!cart) {
    return null;
  }

  return (
    <div>
      <h2>장바구니</h2>
      <CartView cart={cart} />
    </div>
  );
}
```

Hook을 만든다.

```tsx
// src/hooks/useFetchCart..ts

export default function useFetchCart() {
  const store = container.resolve(CartStore);

  const [{ cart }] = useStore(store);

  useEffectOnce(() => {
    store.fetchCart();
  });

  return { cart };
}
```

Store도 만든다.

```tsx
@singleton()
@Store()
class CartStore {
  cart: Cart | null = null;

  async fetchCart() {
    this.setCart(null);

    const cart = await apiService.fetchCart();

    this.setCart(cart);
  }

  @Action()
  setCart(cart: Cart | null) {
    this.cart = cart;
  }
}

export default CartStore
```

여기서는 그냥 null을 썼는데, `ProductDetail`처럼 Null Object를 만들어서 쓰면 더 좋다.

API Service에 `fetchCart` 추가.

```tsx
// src/services/ApiService.ts

async fetchCart(): Promise<Cart> {
  const { data } = await this.instance.get('/cart');
  return data;
}
```

다시 컴포넌트로 돌아와서 보면, `Cart` 타입과 `Cart` 컴포넌트의 이름이 겹치는 문제가 있다. 이 문제에 대한 쉬운 해법으로는 다음 두 가지를 고려할 수 있다:

1. `Cart` 타입을 가져올 때 `as CartType`을 써서 타입의 이름을 바꾼다.
2. `Cart` 컴포넌트를 `CartView` 등 다른 이름으로 바꾼다.

여기서는 컴포넌트 이름을 바꿔서 만들어 보자.

```tsx
// src/components/CartView.tsx

const Container = styled.div`
  table {
    width: 100%;
  }

  th, td {
    padding: .5rem;
    text-align: left;
  }
`;

type CartViewProps = {
  cart: Cart;
};

export default function CartView({ cart }: CartViewProps) {
  if (!cart.lineItems.length) {
    return (
      <p>장바구니가 비었습니다</p>
    );
  }

  return (
    <Container>
      <table>
        <thead>
          <tr>
            <th>제품</th>
            <th>단가</th>
            <th>수량</th>
            <th>금액</th>
          </tr>
        </thead>
        <tbody>
          {cart.lineItems.map((lineItem) => (
            <LineItemView
              key={lineItem.id}
              lineItem={lineItem}
            />
          ))}
        </tbody>
        <tfoot>
          <tr>
            <th colSpan={3}>
              합계
            </th>
            <td>
              {numberFormat(cart.totalPrice)}
              원
            </td>
          </tr>
        </tfoot>
      </table>
    </Container>
  );
}
```

`LineItem`도 마찬가지로 `LineItemView`란 컴포넌트로 만든다.

```tsx
// src/components/cart/LineItemView.tsx

type LineItemViewProps = {
  lineItem: LineItem;
};

export default function LineItemView({ lineItem }: LineItemViewProps) {
  return (
    <tr>
      <td>
        <Link to={`/products/${lineItem.product.id}`}>
          {lineItem.product.name}
        </Link>
        <Options options={lineItem.options} />
      </td>
      <td>
        {numberFormat(lineItem.unitPrice)}
        원
      </td>
      <td>{lineItem.quantity}</td>
      <td>
        {numberFormat(lineItem.totalPrice)}
        원
      </td>
    </tr>
  );
}
```

`Options`컴포넌트는 그냥 이 이름 그대로 가도 될 것 같다.

```tsx
// src/components/cart/Options

const Container = styled.div`
  margin-top: .5rem;
  font-size: 1.4rem;
`;

type OptionsProps = {
  options: OrderOption[];
}

export default function Options({ options }: OptionsProps) {
  if (!options.length) {
    return null;
  }

  const text = options
    .map((option) => `${option.name}: ${option.item.name}`)
    .join(', ');

  return (
    <Container>
      (
      {text}
      )
    </Container>
  );
}
```

cURL을 이용해서 임의로 장바구니에 상품 담기 (카트 화면 확인용)

```bash
curl -X POST https://shop-demo-api-01.fly.dev/cart/line-items \
  -H "Content-Type: application/json" \
  --data '
    {
      "productId": "0BV000PRO0001",
      "options": [
        {
          "id": "0BV000OPT0001",
          "itemId": "0BV000ITEM001"
        },
        {
          "id": "0BV000OPT0002",
          "itemId": "0BV000ITEM005"
        }
      ],
      "quantity": 1
    }
  '
```

```bash
// 확인용 카트 없애기

https://shop-demo-api-01.fly.dev/backdoor/setup-database
```
