# 2. 배송 정보 입력

## 주문 페이지

일단 주문 페이지와 주문 완료 페이지를 추가해서 전체 흐름을 만들자.\
`OrderPage`부터 간단히 만든다. 로직 자체는 장바구니와 매우 유사하다.

```tsx
// src/pages/OrderPage.tsx

export default function OrderPage() {
  const { cart } = useFetchCart();

  if (!cart) {
    return null;
  }

  return (
    <OrderForm cart={cart} />
  );
}
```

`OrderForm` 컴포넌트도 장바구니와 유사하다.

```tsx
// src/components/new-order/OrderForm.tsx

const Container = styled.div`
  h2 {
    font-size: 4rem;
  }
`;

type OrderFormProps = {
  cart: Cart;
};

export default function OrderForm({ cart }: OrderFormProps) {
  return (
    <Container>
      <h2>주문</h2>
      <Table
        lineItems={cart.lineItems}
        totalPrice={cart.totalPrice}
      />
      {/* TODO: 배송지 입력 */}
      {/* TODO: 결제 */}
    </Container>
  );
}
```

`OrderCompletePage`도 `SignupCompletePage`와 마찬가지로 간단하게 구성한다.

```tsx
// src/pages/OrderCompletePage.tsx

export default function OrderCompletePage() {
  return (
    <div>
      <p>
        주문이 완료되었습니다.
      </p>
      <p>
        <Link to="/orders">주문 목록 확인</Link>
      </p>
    </div>
  );
}
```

`routes`에 페이지 두 개를 모두 추가한다.

```tsx
// src/routes.tsx

{ path: '/order', element: <OrderPage /> },
{ path: '/order/complete', element: <OrderCompletePage /> },
```

장바구니 페이지에서 주문 페이지로 갈 수 있도록 `CartView`에 버튼 추가.

```tsx
// src/components/cart/CartView.tsx

export default function CartView({ cart }: CartViewProps) {
  const navigate = useNavigate();

  if (!cart.lineItems.length) {
    return (
      <p>장바구니가 비었습니다</p>
    );
  }

  const handleClick = () => {
    navigate('/order');
  };

  return (
    <div>
      <Table
        lineItems={cart.lineItems}
        totalPrice={cart.totalPrice}
      />
      <Button onClick={handleClick}>
        주문하기
      </Button>
    </div>
  );
}
```

## 배송지 정보 입력

받는 사람 이름, 주소, 전화번호를 입력 받는 폼이 필요하다.
OrderFormStore를 만들어서 관련 정보를 다룰 수 있게 준비하자.
주소는 크게 두 부분으로 나뉘고, 상세 주소가 아닌 상위 주소를 지정할 때 우편번호를 함께 지정하게 한다.

```tsx
// src/stores/OrderFormStore.ts

@singleton()
@Store()
class OrderFormStore {
  name = '';

  address1 = '';

  address2 = '';

  postalCode = '';

  phoneNumber = '';

  get valid() {
    return !!this.name.trim()
      && !!this.address1.trim()
      && !!this.address2.trim()
      && !!this.postalCode.trim()
      && !!this.phoneNumber.trim();
  }

  @Action()
  changeName(name: string) {
    this.name = name;
  }

  @Action()
  changeAddress1(address1: string, postalCode: string) {
    this.address1 = address1;
    this.postalCode = postalCode;
  }

  @Action()
  changeAddress2(address2: string) {
    this.address2 = address2;
  }

  @Action()
  changePhoneNumber(phoneNumber: string) {
    this.phoneNumber = phoneNumber.replace(/[^0-9]/g, '');
  }
}

export default OrderFormStore;
```

늘 하던 것처럼 Store를 쓸 수 있는 Hook을 만든다.

```tsx
// src/hooks/useOrderFormStore.ts

import { container } from 'tsyringe';

import { useStore } from 'usestore-ts';

import OrderFormStore from '../stores/OrderFormStore';

export default function useOrderFormStore() {
  const store = container.resolve(OrderFormStore);
  return useStore(store);
}
```

`OrderForm`에서 쓸 수 있는 `ShippingForm`을 만들자.

```tsx
// src/new-order/ShippingForm.tsx

import styled from 'styled-components';

import TextBox from '../ui/TextBox';
import Button from '../ui/Button';

import useOrderFormStore from '../../hooks/useOrderFormStore';

const Container = styled.div`
  h3 {
    font-size: 2rem;
  }

  input {
    width: 50rem;
  }
`;

const PostalCodeField = styled.div`
  > div {
    display: inline-block;
    margin-right: 1rem;
  }

  input {
    width: 10rem;
  }
`;

export default function ShippingForm() {
  const [{
    name, address1, address2, postalCode, phoneNumber,
  }, store] = useOrderFormStore();

  const handleClickSearchPostalCode = () => {
    // TODO: 우편번호 검색
  };

  const handleChangeName = (value: string) => {
    store.changeName(value);
  };

  const handleChangeAddress1 = (value: {
    address: string;
    postalCode: string;
  }) => {
    store.changeAddress1(value.address, value.postalCode);
  };

  const handleChangeAddress2 = (value: string) => {
    store.changeAddress2(value);
  };

  const handlePhoneNumber = (value: string) => {
    store.changePhoneNumber(value);
  };

  return (
    <Container>
      <h3>받는 사람</h3>
      <TextBox
        label="이름"
        placeholder="받는 분 이름"
        value={name}
        onChange={handleChangeName}
      />
      <PostalCodeField>
        <TextBox
          label="우편번호"
          value={postalCode}
          readOnly
        />
        <Button onClick={handleClickSearchPostalCode}>
          우편번호 검색
        </Button>
      </PostalCodeField>
      <TextBox
        label="주소"
        value={address1}
        readOnly
      />
      <TextBox
        label="상세 주소"
        value={address2}
        onChange={handleChangeAddress2}
      />
      <TextBox
        label="전화번호"
        type="tel"
        value={phoneNumber}
        onChange={handlePhoneNumber}
      />
    </Container>
  );
}
```

`TextBox`에 readOnly 속성을 추가하고, onChange를 옵셔널하게 만든다.

```tsx
// src/components/ui/TextBox.tsx

type TextBoxProps = {
  label: string;
  placeholder?: string;
  type?: 'text' | 'number' | 'password' | 'tel'; // ...and more types...
  value: string;
  onChange?: (value: string) => void;
  readOnly?: boolean;
}

export default function TextBox({
  label, placeholder = undefined, type = 'text', value,
  onChange = undefined, readOnly = false,
}: TextBoxProps) {
  const id = useRef(`textbox-${Math.random().toString().slice(2)}`);

  const handleChange = (event: React.ChangeEvent<HTMLInputElement>) => {
    if (!onChange) {
      return;
    }
    onChange(event.target.value);
  };

  return (
    <Container>
      <label htmlFor={id.current}>
        {label}
      </label>
      <input
        id={id.current}
        type={type}
        placeholder={placeholder}
        value={value}
        onChange={handleChange}
        readOnly={readOnly}
      />
    </Container>
  );
}
```

## 우편번호 검색

우편번호 검색 기능을 사용하는 이유:

주소는 외우지만 우편번호는 못 외우고 있다.
더 중요한 건! → 정확히 표준화된 주소를 얻을 수 있다.
Daum 우편번호 서비스
`index.html` 파일에 스크립트 태그를 추가한다.

```html
<!-- index.html -->

<script src="https://t1.daumcdn.net/mapjsapi/bundle/postcode/prod/postcode.v2.js"></script>
```

전역으로 얻는 daum 객체의 타입을 알 수 있도록 `daum.postcode.d.ts` 파일을 만든다.

```tsx
// src/components/new-order/daum.postcode.d.ts

declare namespace daum {
  export type PostcodeResult = {
    address: string;
    zonecode: string;
  }

  export class Postcode {
    constructor({ oncomplete, width, height }: {
      oncomplete: (data: PostcodeResult) => void;
      width: string;
      height: string;
    });

    embed(element: HTMLElement | null);
  }
}
```

우편번호 검색 모달(modal)을 보여주는 `AddressSearch` 컴포넌트를 만든다. `useRef` 훅을 사용하면 DOM 객체에 직접 접근할 수 있다.

```tsx
// src/components/new-order/AddressSearch.ts

import { useRef } from 'react';

import { useEffectOnce } from 'usehooks-ts';

import styled from 'styled-components';

const Container = styled.div`
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, .5);

  div {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 80%;
    height: 80%;
    max-width: 50rem;
    background: ${(props) => props.theme.colors.background};
  }
`;

type AddressSearchProps = {
  close: ()=> void;
  changeAddress: ({ address, postalCode }: {
    address: string;
    postalCode: string;
  }) => void;
}

export default function AddressSearch({
  close, changeAddress,
}: AddressSearchProps) {
  const refElement = useRef<HTMLDivElement>(null);

  useEffectOnce(() => {
    new daum.Postcode({
      oncomplete(data) {
        const { address, zonecode: postalCode } = data;
        changeAddress({ address, postalCode });
        close();
      },
      width: '100%',
      height: '100%',
    }).embed(refElement.current);
  });

  return (
    <Container id="address-search-container" onClick={close}>
      <div ref={refElement} />
    </Container>
  );
}
```

`ShippingForm`에서 `AddressSearch`를 활용하게 한다.

```tsx
// src/components/new-order/ShippingForm.tsx

const {
  value: searching, setTrue: openSearch, setFalse: closeSearch,
} = useBoolean();

// …(중략)…

const handleClickSearchPostalCode = () => {
  openSearch();
};

// …(중략)…

{searching && (
  <AddressSearch
    close={closeSearch}
    changeAddress={handleChangeAddress1}
  />
)}
```
