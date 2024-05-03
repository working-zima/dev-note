# 4. 배송 및 결제 정보 전달

## 배송 및 결제 정보 전달

`ApiService`에 `createOrder` 메서드를 추가한다.

```tsx
// src/services/ApiService.ts

async createOrder({ receiver, payment }: {
  receiver: {
    name: string;
    address1: string;
    address2: string;
    postalCode: string;
    phoneNumber: string;
  };
  payment: {
    merchantId: string;
    transactionId: string;
  };
}): Promise<void> {
  await this.instance.post('/orders', { receiver, payment });
}
```

`OrderFormStore`에 `order` 메서드를 추가한다.

```tsx
// src/stores/OrderFormStore.ts

async order({ merchantId, transactionId }: {
  merchantId: string;
  transactionId: string;
}) {
  await apiService.createOrder({
    receiver: {
      name: this.name,
      address1: this.address1,
      address2: this.address2,
      postalCode: this.postalCode,
      phoneNumber: this.phoneNumber,
    },
    payment: { merchantId, transactionId },
  });
}
```

B/E로 주문 및 결제 정보 전달

```tsx
// src/components/new-order/PaymentButton.tsx

const [{ valid }, store] = useOrderFormStore();

const handleClick = async () => {
  setError('');

  try {
    const { merchantId, transactionId } = await requestPayment();

    await store.order({ merchantId, transactionId });

    navigate('/order/complete');
  } catch (e) {
    if (e instanceof Error) {
      setError(e.message);
    }
  }
};
```
