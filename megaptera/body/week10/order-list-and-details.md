# 주문 목록 & 주문 상세

## 주문 목록 확인

주문 목록 API에 맞춘 types 추가.

```tsx
// src/types.ts

export type OrderSummary = {
  id: string;
  title: string;
  totalPrice: number;
  status: string;
  orderedAt: string;
}
```

`ApiService`에 `fetchOrders` 메서드 추가.

```tsx
// src/services/ApiService.ts

async fetchOrders(): Promise<OrderSummary[]> {
  const { data } = await this.instance.get('/orders');
  const { orders } = data;
  return orders;
}
```

`OrderListStore` 추가.

```tsx
// src/stores/OrderListStore.ts

@singleton()
@Store()
class OrderListStore {
  orders: OrderSummary[] = [];

  async fetchOrders() {
    this.setOrders([]);

    const orders = await apiService.fetchOrders();

    this.setOrders(orders);
  }

  @Action()
  setOrders(orders: OrderSummary[]) {
    this.orders = orders;
  }
}

export default OrderListStore;
```

훅도 만들어준다.

```tsx
// src/hooks/useFetchOrders.ts

export default function useFetchOrders() {
  const store = container.resolve(OrderListStore);

  const [{ orders }] = useStore(store);

  useEffect(() => {
    store.fetchOrders();
  }, [store]);

  return { orders };
}
```

`OrderListPage` 생성.

```tsx
// src/page/OrderListPage.ts
export default function OrderListPage() {
  const { orders } = useFetchOrders();

  return (
    <div>
      <h2>주문 목록</h2>
      <Orders orders={orders} />
    </div>
  );
}
```

`routes`에 페이지 추가.

```tsx
// src/routes.tsx

{ path: '/orders', element: <OrderListPage /> },
```

`Header`에 링크 추가.

```tsx
// src/components/Header.tsx

<li>
  <Link to="/orders">Orders</Link>
</li>
```

Orders 컴포넌트 작성.

```tsx
// src/components/order-list/Orders.tsx

const Container = styled.div`
  li {
    margin-block: .5rem;
    padding: 1rem;
    background: #EEE;
  }

  a {
    display: block;
    text-decoration: none;
  }
`;

type OrdersProps = {
  orders: OrderSummary[];
}

export default function Orders({ orders }: OrdersProps) {
  if (!orders.length) {
    return null;
  }

  return (
    <Container>
      <ul>
        {orders.map((order) => (
          <li key={order.id}>
            <Link to={`/orders/${order.id}`}>
              <Order order={order} />
            </Link>
          </li>
        ))}
      </ul>
    </Container>
  );
}
```

`Order` 컴포넌트 작성.

```tsx
// src/components/order-list/Order.tsx

const Container = styled.div`
  line-height: 1.5;
`;

type OrderProps = {
  order: OrderSummary;
}

export default function Order({ order }: OrderProps) {
  return (
    <Container>
      <div>
        주문 일시:
        {' '}
        {order.orderedAt}
      </div>
      <div>
        주문 코드:
        {' '}
        {order.id}
      </div>
      <div>
        상품:
        {' '}
        {order.title}
      </div>
      <div>
        결제 금액:
        {' '}
        {numberFormat(order.totalPrice)}
        원
      </div>
    </Container>
  );
}
```

## 주문 상세 확인

주문 상세 API에 맞춘 types 추가.

```tsx
// src/types.ts

export type OrderDetail = {
  id: string;
  title: string;
  lineItems: LineItem[];
  totalPrice: number;
  status: string;
  orderedAt: string;
}
```

`ApiService`에 fetchOrder 메서드 추가.

```tsx
// src/services/ApiService.ts

async fetchOrder({ orderId }: {
  orderId: string;
}): Promise<OrderDetail> {
  const { data } = await this.instance.get(`/orders/${orderId}`);
  return data;
}
```

OrderDetailStore 추가.

```tsx
// src/stores/OrderDetailStore.ts

@singleton()
@Store()
class OrderDetailStore {
  order: OrderDetail = nullOrderDetail;

  loading = true;

  error = false;

  async fetchOrder({ orderId }: {
    orderId: string;
  }) {
    this.startLoading();

    try {
      const order = await apiService.fetchOrder({ orderId });
      this.setOrder(order);
    } catch {
      this.setError();
    }
  }

  @Action()
  private startLoading() {
    this.order = nullOrderDetail;
    this.loading = true;
    this.error = false;
  }

  @Action()
  private setOrder(order: OrderDetail) {
    this.order = order;
    this.loading = false;
    this.error = false;
  }

  @Action()
  private setError() {
    this.order = nullOrderDetail;
    this.loading = false;
    this.error = true;
  }
}

export default OrderDetailStore;
```

Null Object를 준비한다.

```tsx
// src/types.ts

export const nullOrderDetail: OrderDetail = {
  id: '',
  title: '',
  status: '',
  lineItems: [],
  totalPrice: 0,
  orderedAt: '',
};
```

주문 상세 정보를 얻는 Hook을 만든다.

```tsx
// src/hooks/useFetchOrder.ts

export default function useFetchOrder({ orderId }: {
  orderId: string;
}) {
  const store = container.resolve(OrderDetailStore);

  const [{ order, loading, error }] = useStore(store);

  useEffect(() => {
    store.fetchOrder({ orderId });
  }, [store]);

  return { order, loading, error };
}
```

`OrderDetailPage`를 만든다.

```tsx
// src/page/OrderDetailPage.ts

export default function OrderDetailPage() {
  const params = useParams();

  const { order, loading, error } = useFetchOrder({
    orderId: String(params.id),
  });

  if (loading) {
    return (
      <p>Loading...</p>
    );
  }

  if (error) {
    return (
      <p>Error!</p>
    );
  }

  return (
    <Order order={order} />
  );
}
```

`routes`에 페이지 추가.

```tsx
// src/routes.tsx

{ path: '/orders/:id', element: <OrderDetailPage /> },
```

`Order` 컴포넌트를 만든다.

```tsx
// src/components/order-detail/Order.ts

const Container = styled.div`
  dl {
    display: flex;
    flex-wrap: wrap;
    line-height: 1.7;

    dt {
      width: 8rem;
    }

    dd {
      width: calc(100% - 8rem);
    }
  }
`;

type OrderProps = {
  order: OrderDetail;
};

export default function Order({ order }: OrderProps) {
  if (!order.lineItems.length) {
    return null;
  }

  return (
    <Container>
      <dl>
        <dt>주문 일시</dt>
        <dd>{order.orderedAt}</dd>
        <dt>주문 코드</dt>
        <dd>{order.id}</dd>
      </dl>
      <Table
        lineItems={order.lineItems}
        totalPrice={order.totalPrice}
      />
    </Container>
  );
}
```

`LineItem`을 이용해 상품 목록을 보여주는 부분은 장바구니 보여줄 때 만든 `LineItemView`와 `Options` 컴포넌트를 재사용하면 좋을 것 같다.\
`CartView` 컴포넌트에서 `Table` 컴포넌트를 추출하자.

```tsx
// src/components/cart/CartView.ts

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
    <Table
      lineItems={cart.lineItems}
      totalPrice={cart.totalPrice}
    />
  );
}
```

`Table` 컴포넌트는 다음과 같이 추출된다.

```tsx
// src/components/line-item/Table.ts

const Container = styled.div`
  table {
    margin-block: 1rem;
    width: 100%;
  }

  th, td {
    padding: .5rem;
    text-align: left;
  }
`;

type TableProps = {
  lineItems: LineItem[];
  totalPrice: number;
};

export default function Table({ lineItems, totalPrice }: TableProps) {
  if (!lineItems.length) {
    return null;
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
          {lineItems.map((lineItem) => (
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
              {numberFormat(totalPrice)}
              원
            </td>
          </tr>
        </tfoot>
      </table>
    </Container>
  );
}
```
