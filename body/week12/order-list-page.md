# 4.주문 관리

원래 쇼핑몰을 만들 땐 주문 관리가 제일 복잡하지만(특히 백엔드), 여기서는 주문 관리 기능을 아주 간단히 다뤄보자.

## 주문 목록

주문 목록을 확인하는 `OrderListPage`를 만든다.\
여기선 아주 간단히 할 거라, 조금 양이 많은 사용자 또는 카테고리 목록에 불과하다.

```tsx
import { Link } from 'react-router-dom';

import styled from 'styled-components';

import useFetchOrders from '../hooks/useFetchOrders';

import numberFormat from '../utils/numberFormat';

import { STATUS_MESSAGES } from '../constants';

const Container = styled.div`
  h2 {
    margin-bottom: 2rem;
    font-size: 2rem;
  }

  table {
    th, td {
      padding: 1rem;
      text-align: left;
    }

    th {
      border-bottom: .1rem solid ${(props) => props.theme.colors.secondary}
    }

    img {
      width: 10rem;
      vertical-align: middle;
    }

    a {
      display: block;
      margin-bottom: 1rem;
    }
  }

  > a {
    display: inline-block;
    margin-block: 1rem;
  }
`;

export default function OrderListPage() {
  const { orders, loading, error } = useFetchOrders();

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
    <Container>
      <h2>Orders</h2>
      <table>
        <thead>
          <tr>
            <th>주문일</th>
            <th>주문자</th>
            <th>상품</th>
            <th>총 가격</th>
            <th>상태</th>
            <th>행동</th>
          </tr>
        </thead>
        <tbody>
          {orders.map((order) => (
            <tr key={order.id}>
              <td>{order.orderedAt}</td>
              <td>{order.orderer.name}</td>
              <td>{order.title}</td>
              <td>
                {numberFormat(order.totalPrice)}
                원
              </td>
              <td>{STATUS_MESSAGES[order.status]}</td>
              <td>
                <Link to={`/orders/${order.id}`}>
                  자세히
                </Link>
                <Link to={`/orders/${order.id}/edit`}>
                  상태 변경
                </Link>
              </td>
            </tr>
          ))}
        </tbody>
      </table>
    </Container>
  );
}
```

`useFetchOrders` 훅을 만들자.

```tsx
import useFetch from './useFetch';

import { OrderSummary } from '../types';

export default function useFetchOrders() {
  const { data, error, loading } = useFetch<{
    orders: OrderSummary[];
  }>('/orders');

  return {
    orders: data?.orders ?? [],
    error,
    loading,
  };
}
주문 상태를 어떻게 표시할지 상수만 모은 contants.ts 파일에서 정의하자.

export const STATUS_MESSAGES: Record<string, string> = {
  paid: '결제 완료',
  ready: '배송 준비',
  shipping: '배송 중',
  complete: '배송 완료',
  canceled: '취소',
};
```

## 주문 상세

주문 상세 정보도 확인해 보자.

```tsx
import { Link, useParams } from 'react-router-dom';

import styled from 'styled-components';

import Options from '../components/Options';

import useFetchOrder from '../hooks/useFetchOrder';

import numberFormat from '../utils/numberFormat';

import { STATUS_MESSAGES } from '../constants';

const Container = styled.div`
  h2 {
    margin-bottom: 2rem;
    font-size: 2rem;
  }

  dl {
    &::after {
      clear: left;
      display: block;
      content: "";
    }

    dt {
      clear: left;
      width: 10rem;
      margin-right: 1rem;
      text-align: right;
    }

    dt, dd {
      float: left;
      margin-block: .5rem;
    }

    span {
      margin-left: .5rem;
      font-size: 1.4rem;
    }
  }

  > a {
    display: inline-block;
    margin-block: 1rem;
  }
`;

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

  if (error || !order) {
    return (
      <p>Error!</p>
    );
  }

  return (
    <Container>
      <h2>Order Detail</h2>
      <dl>
        <dt>주문일시</dt>
        <dd>{order.orderedAt}</dd>
        <dt>주문자</dt>
        <dd>{order.orderer.name}</dd>
        <dt>상품</dt>
        <dd>
          <ul>
            {order.lineItems.map((lineItem) => (
              <li key={lineItem.id}>
                {lineItem.product.name}
                <Options options={lineItem.options} />
              </li>
            ))}
          </ul>
        </dd>
        <dt>총 가격</dt>
        <dd>
          {numberFormat(order.totalPrice)}
          원
        </dd>
        <dt>배송 정보</dt>
        <dd>
          <p>
            받는 사람:
            {' '}
            {order.receiver.name}
          </p>
          <p>
            연락처:
            {' '}
            {order.receiver.phoneNumber}
          </p>
          <p>
            {order.receiver.address1}
            {' '}
            {order.receiver.address2}
            {' '}
            (우편번호:
            {' '}
            {order.receiver.postalCode}
            )
          </p>
        </dd>
        <dt>결제 정보</dt>
        <dd>
          <p>
            주문번호:
            {' '}
            {order.payment.merchantId}
          </p>
          <p>
            결제고유번호:
            {' '}
            {order.payment.transactionId}
          </p>
        </dd>
        <dt>상태</dt>
        <dd>{STATUS_MESSAGES[order.status]}</dd>
      </dl>
      <Link to={`/orders/${order.id}/edit`}>
        상태 변경
      </Link>
    </Container>
  );
}
```

기존과 비슷하게 `useFetchOrder` 훅을 만들자.

```tsx
import useFetch from './useFetch';

import { apiService } from '../services/ApiService';

import { OrderDetail } from '../types';

export default function useFetchOrder({ orderId }: {
  orderId: string;
}) {
  const {
    data, error, loading,
  } = useFetch<OrderDetail>(`/orders/${orderId}`);

  return {
    order: data,
    error,
    loading,
  };
}
```

## 주문 상태 변경

고객 지원 업무를 위해서는 주문을 자세히 다룰 수 있는 기능이 필요하다.\
여기서는 단순히 주문 상태만 변경하는 걸 만들어 보고, 필요에 따라 B/E 개발자와 논의해 계속해서 확장해 나가자.\
예를 들어 “배송 시작” 버튼을 누르면 송장 번호를 입력하게 하고, 이후 “배송 중” 상태로 변경되게 할 수 있다.\
이런 건 모두 단순 CRUD를 넘어서 비즈니스에 대한 이해가 필요한 부분이다.

```tsx
import { useNavigate, useParams } from 'react-router-dom';

import { Controller, useForm } from 'react-hook-form';

import styled from 'styled-components';

import Button from '../components/ui/Button';

import useFetchOrder from '../hooks/useFetchOrder';

import numberFormat from '../utils/numberFormat';

import { STATUS_MESSAGES } from '../constants';

const Container = styled.div`
  h2 {
    margin-bottom: 2rem;
    font-size: 2rem;
  }

  dl {
    &::after {
      clear: left;
      display: block;
      content: "";
    }

    dt {
      clear: left;
      width: 10rem;
      margin-right: 1rem;
      text-align: right;
    }

    dt, dd {
      float: left;
      margin-block: .5rem;
    }

    img {
      width: 10rem;
    }
  }

  [type=submit] {
    display: block;
    margin-block: 1rem;
  }
`;

export default function OrderEditPage() {
  const params = useParams();

  const orderId = String(params.id);

  const navigate = useNavigate();

  const {
    order, loading, error, updateOrder,
  } = useFetchOrder({
    orderId,
  });

  type FormValues = {
    status: string;
  };

  const { handleSubmit, control } = useForm<FormValues>();

  const onSubmit = async (data: FormValues) => {
    await updateOrder({
      status: data.status,
    });
    navigate(`/orders/${orderId}`);
  };

  if (loading) {
    return (
      <p>Loading...</p>
    );
  }

  if (error || !order) {
    return (
      <p>Error!</p>
    );
  }

  return (
    <Container>
      <h2>Order Status Transition</h2>
      <dl>
        <dt>주문일시</dt>
        <dd>{order.orderedAt}</dd>
        <dt>주문자</dt>
        <dd>{order.orderer.name}</dd>
        <dt>상품</dt>
        <dd>{order.title}</dd>
        <dt>총 가격</dt>
        <dd>
          {numberFormat(order.totalPrice)}
          원
        </dd>
      </dl>
      <form onSubmit={handleSubmit(onSubmit)}>
        <Controller
          control={control}
          name="status"
          defaultValue={order.status}
          render={({ field: { onChange, value } }) => (
            <div>
              <label htmlFor="input-status">상태</label>
              <select
                id="input-status"
                value={value}
                onChange={onChange}
              >
                {Object.keys(STATUS_MESSAGES).map((status) => (
                  <option key={status} value={status}>
                    {STATUS_MESSAGES[status]}
                  </option>
                ))}
              </select>
            </div>
          )}
        />
        <Button type="submit">
          변경
        </Button>
      </form>
    </Container>
  );
}
```

`useFetchOrder` 훅에 `updateOrder`를 추가한다.

```tsx
import useFetch from './useFetch';

import { apiService } from '../services/ApiService';

import { OrderDetail } from '../types';

export default function useFetchOrder({ orderId }: {
  orderId: string;
}) {
  const {
    data, error, loading, mutate,
  } = useFetch<OrderDetail>(`/orders/${orderId}`);

  return {
    order: data,
    error,
    loading,
    async updateOrder({ status }: {
      status: string;
    }) {
      await apiService.updateOrder({ orderId, status });

      mutate();
    },
  };
}
```
