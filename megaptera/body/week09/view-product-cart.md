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

## Curl (Client URL)

자바스크립트 환경에서 REST API(http)를 테스트하고싶다면 보통 ajax, fetch 를 이용해 요청을 보냅니다.\
SHELL(커맨드라인 환경)에서 REST API(http) 테스트 하고 싶으면 curl 명령어를 이용하면 됩니다.

### 명령어 옵션

```bash
curl [options...] <url>

curl --help all # 전체 명령어 보기
```

short 형식 | long 형식 | 설명
:-:|:-:|:-:
`-k` | `--insecure` | https 프로토콜에서 SSL 인증서에 대한 검증없이 연결
`-i` | `--head` | HTTP 헤더만 보여주고 컨텐츠는 표시하지 않음
`-D` | `--dump-header <file>` | HTTP 헤더를 file에 기록 (덤프)
`-L` | `--location` | HTTP 301, 302 응답을 받은 경우 리디렉션 URL로 따라간다. `--max-redirs` 옵션 뒤에 숫자로 몇 번의 리디렉션까지 따라갈 것인지를 적을 수 있다. 기본 값은 50이다.
`-d` | `--data` | HTTP POST 요청 데이터 입력
`-J` | `--remote-header-name` | 헤더에 있는 파일 이름으로 다운로드 파일을 저장
`-o` | `--output FILE` | curl로 받아온 내용을 FILE 이라는 이름의 파일로 저장
`-O` | `--remote-name` | 파일 저장시 리모트에 저장되어 있던 이름을 그대로 가져와서 로컬에 저장
`-s` | `--silent` | 진행 내용이나 메시지들을 출력하지 않음HTTP response code 만 가져오거나 할 경우 유리
`-X` | `--request` | 요청시 사용할 메소드의 종류 (GET, POST, PUT, PATCH, DELETE)
`-i` | `--include` | 응답에 Content 만 출력하지 않고 서버의 Reponse 도 포함해서 출력한다. (디버깅에 유용)
`-A` | `--user-agent` | 서버에 User-Agent `<name>` 보내기
`-u` | `--user` | 서버 사용자 및 비밀번호
`-T` | `--upload-file` | 로컬 FILE 을 대상으로 전송
`-f` | `--fail` | HTTP 오류 시 자동으로 실패 (출력 없음)
`-G` |  | 전송할 사이트 url 및 ip 주소
`-H` |  | 전송할 헤더를 지정
`-J` | `--remote-header-name` | 어떤 웹서비스는 파일 다운로드시 Content-Disposition Header 를 파싱해야 정확한 파일이름을 알 수 있을 경우가 있다. `-J` 옵션을 주면 헤더에 있는 파일 이름으로 저장한다.
`-v` | `--verbose` | 동작하면서 자세한 헤더 통신 옵션을 출력한다.
`-C` | `--continue-at` | 파일 다운로드 재개

### 카트 화면 확인

cURL을 이용해서 임의로 장바구니에 상품 담기

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

## 참고 자료

- [🐧 CURL 명령어 사용법 💯 완전 총정리](https://inpa.tistory.com/entry/LINUX-📚-CURL-명령어-사용법-다양한-예제로-정리)
