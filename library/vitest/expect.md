# expect

다음과 같은 타입들이 아래 타입 시그니처에서 사용됩니다:

```ts
type Awaitable<T> = T | PromiseLike<T>;
```

`expect`는 어서션(assertion)을 생성하는 데 사용됩니다. 이 문맥에서 '어서션'은 어떤 조건을 주장하는 함수들을 의미합니다. Vitest는 기본적으로 `chai` 어서션을 제공하며, `chai` 위에 구축된 `Jest` 호환 어서션도 제공합니다. `Jest`와 달리, Vitest는 두 번째 인자로 메시지를 받을 수 있으며, 어서션이 실패했을 때 이 메시지가 에러 메시지로 출력됩니다.

```ts
export interface ExpectStatic
  extends Chai.ExpectStatic,
    AsymmetricMatchersContaining {
  <T>(actual: T, message?: string): Assertion<T>;
  extend: (expects: MatchersObject) => void;
  anything: () => any;
  any: (constructor: unknown) => any;
  getState: () => MatcherState;
  setState: (state: Partial<MatcherState>) => void;
  not: AsymmetricMatchersContaining;
}
```

예를 들어, 아래 코드는 `input` 값이 `2`와 같다는 것을 어서션으로 주장합니다. 만약 같지 않다면, 어서션은 에러를 던지고 테스트는 실패하게 됩니다.

```ts
import { expect } from "vitest";

const input = Math.sqrt(4);

expect(input).to.equal(2); // chai API
expect(input).toBe(2); // jest API
```

기술적으로 이 예시는 [`test`](/api/#test) 함수를 사용하지 않기 때문에, 콘솔에서 Node.js 에러가 보이게 되며 Vitest의 출력이 아닙니다. `test`에 대해 더 알고 싶다면 [Test API Reference](/api/)를 읽어보세요.

또한 `expect`는 정적 방식으로 사용하여 나중에 설명할 매처 함수들에 접근할 수 있습니다.

> ⚠️ `expect`는 타입 테스트에는 아무런 영향을 주지 않습니다. 타입 에러가 없는 표현식에 대해서는 작동하지 않습니다. Vitest를 [타입 체커](/guide/testing-types)로 사용하고 싶다면 [`expectTypeOf`](/api/expect-typeof) 또는 [`assertType`](/api/assert-type) 을 사용하세요.

## soft

- **타입:** `ExpectStatic & (actual: any) => Assertions`

`expect.soft`는 `expect`와 유사하게 작동하지만, 어서션이 실패해도 테스트 실행을 중단하지 않고 계속 진행합니다. 테스트 완료 시까지 발생한 모든 오류가 출력됩니다.

```ts
import { expect, test } from "vitest";

test("expect.soft test", () => {
  expect.soft(1 + 1).toBe(3); // 실패로 표시되지만 계속 진행
  expect.soft(1 + 2).toBe(4); // 실패로 표시되지만 계속 진행
});
// 리포터는 테스트가 끝난 후 두 오류를 모두 보고함
```

`expect`와 함께 사용할 수도 있습니다. 이 경우 `expect` 어서션이 실패하면 테스트가 즉시 종료되며, 그 전에 발생한 모든 오류가 출력됩니다.

```ts
import { expect, test } from "vitest";

test("expect.soft test", () => {
  expect.soft(1 + 1).toBe(3); // 실패로 표시되지만 계속 진행
  expect(1 + 2).toBe(4); // 실패 시 테스트 종료, 이전 오류 출력
  expect.soft(1 + 3).toBe(5); // 실행되지 않음
});
```

> ⚠️ `expect.soft`는 [`test`](/api/#test) 함수 내부에서만 사용할 수 있습니다.

## poll

```ts
interface ExpectPoll extends ExpectStatic {
  (actual: () => T, options: { interval; timeout; message }): Promise<
    Assertions<T>
  >;
}
```

`expect.poll`은 _어떤 어서션이 통과할 때까지_ 계속 반복해서 실행합니다. `interval`과 `timeout` 옵션을 설정하여 몇 번이나 `expect.poll` 콜백을 재시도할지 설정할 수 있습니다.

만약 콜백 내부에서 오류가 발생하면, Vitest는 `timeout`이 끝날 때까지 계속 재시도합니다.

```ts
import { expect, test } from "vitest";

test("element exists", async () => {
  asyncInjectElement();

  await expect.poll(() => document.querySelector(".element")).toBeTruthy();
});
```

> ⚠️ `expect.poll`은 모든 어서션을 비동기화합니다. 따라서 반드시 `await` 키워드와 함께 사용해야 합니다.\
> Vitest 3부터는 `await`를 빠뜨리면 경고 메시지를 출력하며,\
> Vitest 4부터는 어서션을 `await`하지 않으면 테스트가 실패로 처리됩니다.
> ❌ 아래 matcher들과는 함께 사용할 수 없습니다:
>
> - **스냅샷 매처**: 항상 통과하므로 지원되지 않음.
> - **`.resolves`, `.rejects`**: `expect.poll`은 이미 비동기 처리를 포함하고 있음.
> - **`toThrow`, `toThrowError` 등 예외 어서션**: `expect.poll`은 값을 미리 평가하므로 예외를 감지할 수 없음.

### 대안: `vi.waitFor` 사용

```ts
import { expect, vi } from "vitest";

const flakyValue = await vi.waitFor(() => getFlakyValue());
expect(flakyValue).toMatchSnapshot();
```

## not

`not`을 사용하면 어서션을 부정할 수 있습니다. 예를 들어, 아래 코드는 `input` 값이 `2`가 아니라고 주장합니다. 만약 `2`라면 테스트는 실패합니다.

```ts
import { expect, test } from "vitest";

const input = Math.sqrt(16);

expect(input).not.to.equal(2); // chai API
expect(input).not.toBe(2); // jest API
```

## toBe

- **타입:** `(value: any) => Awaitable<void>`

`toBe`는 원시 값이 같은지 또는 객체의 참조가 동일한지를 검사합니다. 이는 `Object.is(3, 3)`을 검사하는 것과 동일합니다. 객체의 구조가 같은지 비교하려면 `toEqual`을 사용하세요.

예를 들어, 아래 코드는 트레이더가 사과 13개를 갖고 있는지 확인합니다.

```ts
import { expect, test } from "vitest";

const stock = {
  type: "apples",
  count: 13,
};

test("stock has 13 apples", () => {
  expect(stock.type).toBe("apples");
  expect(stock.count).toBe(13);
});

test("stocks are the same", () => {
  const refStock = stock; // 같은 참조

  expect(stock).toBe(refStock);
});
```

> ⚠️ `toBe`는 부동소수점 비교에는 사용하지 마세요. JavaScript에서 부동소수점은 반올림되므로 `0.1 + 0.2 !== 0.3` 입니다.\
> 대신 [`toBeCloseTo`](#tobecloseto)를 사용하세요.

## toBeCloseTo

- **타입:** `(value: number, numDigits?: number) => Awaitable<void>`

`toBeCloseTo`는 부동소수점 숫자들을 비교합니다. `numDigits` 인자는 소수점 아래 비교할 자릿수를 제한합니다.

```ts
import { expect, test } from "vitest";

test.fails("decimals are not equal in javascript", () => {
  expect(0.2 + 0.1).toBe(0.3); // 0.30000000000000004와 비교되어 실패
});

test("decimals are rounded to 5 after the point", () => {
  expect(0.2 + 0.1).toBeCloseTo(0.3, 5); // 성공
  expect(0.2 + 0.1).not.toBeCloseTo(0.3, 50); // 실패
});
```

## toBeDefined

- **타입:** `() => Awaitable<void>`

`toBeDefined`는 값이 `undefined`가 아님을 확인합니다. 함수가 어떤 값을 반환했는지 확인할 때 유용합니다.

```ts
import { expect, test } from "vitest";

function getApples() {
  return 3;
}

test("function returned something", () => {
  expect(getApples()).toBeDefined();
});
```

## toBeUndefined

- **타입:** `() => Awaitable<void>`

`toBeUndefined`는 값이 `undefined`임을 확인합니다. 함수가 값을 반환하지 않았는지 확인할 때 유용합니다.

```ts
import { expect, test } from "vitest";

function getApplesFromStock(stock: string) {
  if (stock === "Bill") {
    return 13;
  }
}

test("mary doesn't have a stock", () => {
  expect(getApplesFromStock("Mary")).toBeUndefined();
});
```

## toBeTruthy

- **타입:** `() => Awaitable<void>`

`toBeTruthy`는 값이 `boolean`으로 변환했을 때 `true`가 되는지를 확인합니다.\
값 자체가 중요하지 않고, 단지 "참"인지 확인하고 싶을 때 유용합니다.

예를 들어, `stocks.getInfo`의 반환값이 무엇이든 관계없이, 참이라면 사과를 판매합니다.

```ts
import { Stocks } from "./stocks.js";

const stocks = new Stocks();
stocks.sync("Bill");
if (stocks.getInfo("Bill")) {
  stocks.sell("apples", "Bill");
}
```

위의 동작을 테스트하려면 다음과 같이 작성할 수 있습니다:

```ts
import { expect, test } from "vitest";
import { Stocks } from "./stocks.js";

const stocks = new Stocks();

test("if we know Bill stock, sell apples to him", () => {
  stocks.sync("Bill");
  expect(stocks.getInfo("Bill")).toBeTruthy();
});
```

> 참고: JavaScript에서 falsy한 값은 다음과 같습니다:
>
> `false`, `null`, `undefined`, `NaN`, `0`, `-0`, `0n`, `""`, `document.all`\
> 그 외의 모든 값은 truthy입니다.

## toBeFalsy

- **타입:** `() => Awaitable<void>`

`toBeFalsy`는 값이 `boolean`으로 변환했을 때 `false`가 되는지를 확인합니다.\
값 자체가 중요하지 않고, 단지 "거짓"인지 확인하고 싶을 때 유용합니다.

예를 들어, `stocks.stockFailed`의 반환값이 어떤 falsy 값이든 괜찮은 상황에서 다음처럼 코드를 작성할 수 있습니다.

```ts
import { Stocks } from "./stocks.js";

const stocks = new Stocks();
stocks.sync("Bill");
if (!stocks.stockFailed("Bill")) {
  stocks.sell("apples", "Bill");
}
```

위 동작을 테스트하려면 이렇게 작성할 수 있습니다:

```ts
import { expect, test } from "vitest";
import { Stocks } from "./stocks.js";

const stocks = new Stocks();

test("if Bill stock hasn't failed, sell apples to him", () => {
  stocks.syncStocks("Bill");
  expect(stocks.stockFailed("Bill")).toBeFalsy();
});
```

> 참고: JavaScript의 falsy 값은 위와 동일합니다.

## toBeNull

- **타입:** `() => Awaitable<void>`

`toBeNull`은 값이 `null`인지 확인합니다.\
이는 `.toBe(null)`의 별칭입니다.

```ts
import { expect, test } from "vitest";

function apples() {
  return null;
}

test("we don't have apples", () => {
  expect(apples()).toBeNull();
});
```

## toBeNaN

- **타입:** `() => Awaitable<void>`

`toBeNaN`은 값이 `NaN`인지 확인합니다.\
이는 `.toBe(NaN)`의 별칭입니다.

```ts
import { expect, test } from "vitest";

let i = 0;

function getApplesCount() {
  i++;
  return i > 1 ? Number.NaN : i;
}

test("getApplesCount has some unusual side effects...", () => {
  expect(getApplesCount()).not.toBeNaN();
  expect(getApplesCount()).toBeNaN();
});
```

## toBeOneOf

- **타입:** `(sample: Array<any>) => any`

`toBeOneOf`는 주어진 배열 안에 값이 포함되어 있는지를 확인합니다.

```ts
import { expect, test } from "vitest";

test("fruit is one of the allowed values", () => {
  expect(fruit).toBeOneOf(["apple", "banana", "orange"]);
});
```

비대칭 매처(asymmetric matcher)는 선택적 속성이 `null`이거나 `undefined`일 수 있는 경우에도 유용하게 사용할 수 있습니다.

```ts
test("optional properties can be null or undefined", () => {
  const user = {
    firstName: "John",
    middleName: undefined,
    lastName: "Doe",
  };

  expect(user).toEqual({
    firstName: expect.any(String),
    middleName: expect.toBeOneOf([expect.any(String), undefined]),
    lastName: expect.any(String),
  });
});
```

> 💡 `expect.not`과 함께 사용하여 값이 배열 안에 포함되지 않았는지를 검사할 수도 있습니다.

## toBeTypeOf

- **타입:** `(c: 'bigint' | 'boolean' | 'function' | 'number' | 'object' | 'string' | 'symbol' | 'undefined') => Awaitable<void>`

`toBeTypeOf`는 주어진 값이 특정 타입인지를 확인합니다.

```ts
import { expect, test } from "vitest";

const actual = "stock";

test("stock is type of string", () => {
  expect(actual).toBeTypeOf("string");
});
```

## toBeInstanceOf

- **타입:** `(c: any) => Awaitable<void>`

`toBeInstanceOf`는 값이 특정 클래스의 인스턴스인지를 확인합니다.

```ts
import { expect, test } from "vitest";
import { Stocks } from "./stocks.js";

const stocks = new Stocks();

test("stocks are instance of Stocks", () => {
  expect(stocks).toBeInstanceOf(Stocks);
});
```

## toBeGreaterThan

- **타입:** `(n: number | bigint) => Awaitable<void>`

`toBeGreaterThan`은 실제 값이 기대 값보다 **큰지** 확인합니다.\
값이 동일하면 테스트는 실패합니다.

```ts
import { expect, test } from "vitest";
import { getApples } from "./stocks.js";

test("have more than 10 apples", () => {
  expect(getApples()).toBeGreaterThan(10);
});
```

## toBeGreaterThanOrEqual

- **타입:** `(n: number | bigint) => Awaitable<void>`

`toBeGreaterThanOrEqual`은 실제 값이 기대 값보다 **크거나 같은지** 확인합니다.

```ts
import { expect, test } from "vitest";
import { getApples } from "./stocks.js";

test("have 11 apples or more", () => {
  expect(getApples()).toBeGreaterThanOrEqual(11);
});
```

## toBeLessThan

- **타입:** `(n: number | bigint) => Awaitable<void>`

`toBeLessThan`은 실제 값이 기대 값보다 **작은지** 확인합니다.\
값이 동일하면 테스트는 실패합니다.

```ts
import { expect, test } from "vitest";
import { getApples } from "./stocks.js";

test("have less than 20 apples", () => {
  expect(getApples()).toBeLessThan(20);
});
```

## toBeLessThanOrEqual

- **타입:** `(n: number | bigint) => Awaitable<void>`

`toBeLessThanOrEqual`은 실제 값이 기대 값보다 **작거나 같은지** 확인합니다.

```ts
import { expect, test } from "vitest";
import { getApples } from "./stocks.js";

test("have 11 apples or less", () => {
  expect(getApples()).toBeLessThanOrEqual(11);
});
```

## toEqual

- **타입:** `(received: any) => Awaitable<void>`

`toEqual`은 실제 값이 기대 값과 같거나, 객체일 경우 구조가 동일한지를 확인합니다.\
객체를 재귀적으로 비교합니다.\
`toBe`는 참조 동등성을 검사하지만, `toEqual`은 값과 구조를 검사합니다.

```ts
import { expect, test } from "vitest";

const stockBill = {
  type: "apples",
  count: 13,
};

const stockMary = {
  type: "apples",
  count: 13,
};

test("stocks have the same properties", () => {
  expect(stockBill).toEqual(stockMary);
});

test("stocks are not the same", () => {
  expect(stockBill).not.toBe(stockMary);
});
```

> ⚠️ `Error` 객체의 경우 `name`, `message`, `cause`, `AggregateError.errors` 등 열거되지 않는 속성도 비교됩니다.\
> `Error.cause`는 비대칭 방식으로 비교됩니다.

```ts
// 성공
expect(new Error("hi", { cause: "x" })).toEqual(new Error("hi"));

// 실패
expect(new Error("hi")).toEqual(new Error("hi", { cause: "x" }));
```

예외가 발생했는지 테스트하려면 `toThrowError`를 사용하세요.

ㄴ## toStrictEqual

- **타입:** `(received: any) => Awaitable<void>`

`toStrictEqual`은 `toEqual`과 유사하지만, 보다 **엄격한 조건**을 검사합니다.

### `toStrictEqual`과 `toEqual`의 차이점

- `undefined` 속성도 검사합니다.  
  예: `{a: undefined, b: 2}`는 `{b: 2}`와 **같지 않음**
- 배열의 희소성(sparseness)도 검사합니다.  
  예: `[ , 1 ]`는 `[undefined, 1]`과 **같지 않음**
- 객체의 타입도 비교합니다.  
  예: 같은 필드를 가진 클래스 인스턴스와 리터럴 객체는 **같지 않음**

```ts
import { expect, test } from "vitest";

class Stock {
  constructor(type) {
    this.type = type;
  }
}

test("structurally the same, but semantically different", () => {
  expect(new Stock("apples")).toEqual({ type: "apples" });
  expect(new Stock("apples")).not.toStrictEqual({ type: "apples" });
});
```

## toContain

- **타입:** `(received: string) => Awaitable<void>`

`toContain`은 실제 값이 배열에 포함되어 있는지 또는 문자열에 포함되어 있는지를 확인합니다.\
브라우저 환경에서는 `classList` 안에 특정 클래스가 있는지 또는 요소가 다른 요소 안에 있는지도 확인할 수 있습니다.

```ts
import { expect, test } from "vitest";
import { getAllFruits } from "./stocks.js";

test("the fruit list contains orange", () => {
  expect(getAllFruits()).toContain("orange");

  const element = document.querySelector("#el");
  // 클래스 포함 여부 확인
  expect(element.classList).toContain("flex");
  // 부모 요소가 자식 요소를 포함하는지 확인
  expect(document.querySelector("#wrapper")).toContain(element);
});
```

## toContainEqual

- **타입:** `(received: any) => Awaitable<void>`

`toContainEqual`은 배열 내에 특정 구조와 값을 가진 객체가 포함되어 있는지를 확인합니다.\
배열의 각 요소에 대해 [`toEqual`](#toequal)을 사용해 검사합니다.

```ts
import { expect, test } from "vitest";
import { getFruitStock } from "./stocks.js";

test("apple available", () => {
  expect(getFruitStock()).toContainEqual({ fruit: "apple", count: 5 });
});
```

## toHaveLength

- **타입:** `(received: number) => Awaitable<void>`

`toHaveLength`는 객체가 `.length` 속성을 가지고 있고, 그 값이 지정한 숫자인지를 확인합니다.

```ts
import { expect, test } from "vitest";

test("toHaveLength", () => {
  expect("abc").toHaveLength(3);
  expect([1, 2, 3]).toHaveLength(3);

  expect("").not.toHaveLength(3); // 길이가 3이 아님
  expect({ length: 3 }).toHaveLength(3); // length 속성이 존재하면 객체도 검사 가능
});
```

## toHaveProperty

- **타입:** `(key: any, received?: any) => Awaitable<void>`

`toHaveProperty`는 객체가 특정 키 경로에 해당하는 속성을 **가지고 있는지**를 확인합니다.\
두 번째 인자를 제공하면 해당 속성의 **값이 기대값과 일치하는지**도 검사합니다.

```ts
import { expect, test } from "vitest";

const invoice = {
  isActive: true,
  "P.O": "12345",
  customer: {
    first_name: "John",
    last_name: "Doe",
    location: "China",
  },
  total_amount: 5000,
  items: [
    {
      type: "apples",
      quantity: 10,
    },
    {
      type: "oranges",
      quantity: 5,
    },
  ],
};

test("John Doe Invoice", () => {
  expect(invoice).toHaveProperty("isActive"); // 키 존재 여부 확인
  expect(invoice).toHaveProperty("total_amount", 5000); // 키와 값 모두 검사

  expect(invoice).not.toHaveProperty("account"); // 존재하지 않는 키 검사

  // 점 표기법을 사용한 깊은 경로 접근
  expect(invoice).toHaveProperty("customer.first_name");
  expect(invoice).toHaveProperty("customer.last_name", "Doe");
  expect(invoice).not.toHaveProperty("customer.location", "India");

  // 배열 표기법을 사용한 깊은 경로 접근
  expect(invoice).toHaveProperty("items[0].type", "apples");
  expect(invoice).toHaveProperty("items.0.type", "apples");

  // 배열을 통한 경로 접근
  expect(invoice).toHaveProperty(["items", 0, "type"], "apples");
  expect(invoice).toHaveProperty(["items", "0", "type"], "apples");

  // 키가 점(.)을 포함할 경우 배열로 감싸서 경로 구분 방지
  expect(invoice).toHaveProperty(["P.O"], "12345");
});
```

## toMatch

- **타입:** `(received: string | RegExp) => Awaitable<void>`

`toMatch`는 문자열이 특정 문자열 또는 정규 표현식과 **일치하는지** 확인합니다.

```ts
import { expect, test } from "vitest";

test("top fruits", () => {
  expect("top fruits include apple, orange and grape").toMatch(/apple/);
  expect("applefruits").toMatch("fruit"); // 문자열도 허용됨
});
```

## toMatchObject

- **타입:** `(received: object | array) => Awaitable<void>`

`toMatchObject`는 객체가 특정 부분 객체와 **부분적으로 일치하는지** 확인합니다.\
배열의 경우, 요소 수까지 정확히 일치해야 합니다.\
이는 `arrayContaining`과 달리 추가 요소를 허용하지 않습니다.

```ts
import { expect, test } from "vitest";

const johnInvoice = {
  isActive: true,
  customer: {
    first_name: "John",
    last_name: "Doe",
    location: "China",
  },
  total_amount: 5000,
  items: [
    {
      type: "apples",
      quantity: 10,
    },
    {
      type: "oranges",
      quantity: 5,
    },
  ],
};

const johnDetails = {
  customer: {
    first_name: "John",
    last_name: "Doe",
    location: "China",
  },
};

test("invoice has john personal details", () => {
  expect(johnInvoice).toMatchObject(johnDetails);
});

test("the number of elements must match exactly", () => {
  expect([{ foo: "bar" }, { baz: 1 }]).toMatchObject([
    { foo: "bar" },
    { baz: 1 },
  ]);
});
```

## toThrowError

- **타입:** `(received: any) => Awaitable<void>`
- **별칭:** `toThrow`

`toThrowError`는 함수가 실행되었을 때 예외를 **던지는지** 확인합니다.

선택적으로 아래의 값 중 하나를 인자로 전달할 수 있습니다:

- `RegExp`: 에러 메시지가 정규식 패턴과 일치하는지
- `string`: 에러 메시지에 해당 문자열이 포함되어 있는지
- `Error`, `AsymmetricMatcher`: `toEqual`과 유사한 객체 비교

> 💡 반드시 **함수를 래핑한 콜백**을 넘겨야 합니다. 그렇지 않으면 에러가 테스트 프레임워크에 잡히지 않고 바로 throw 됩니다.\
> 단, 비동기 함수는 `rejects`와 함께 사용할 경우 래핑 없이 테스트 가능합니다.

```ts
import { expect, test } from "vitest";

function getFruitStock(type: string) {
  if (type === "pineapples") {
    throw new Error("Pineapples are not in stock");
  }
  // 그 외 로직...
}

test("throws on pineapples", () => {
  // "stock"이라는 단어가 포함되었는지 확인
  expect(() => getFruitStock("pineapples")).toThrowError(/stock/);
  expect(() => getFruitStock("pineapples")).toThrowError("stock");

  // 정확한 메시지 일치
  expect(() => getFruitStock("pineapples")).toThrowError(
    /^Pineapples are not in stock$/
  );

  // Error 객체와 일치하는지 확인
  expect(() => getFruitStock("pineapples")).toThrowError(
    new Error("Pineapples are not in stock")
  );

  // 객체 속성으로 에러 내용 확인
  expect(() => getFruitStock("pineapples")).toThrowError(
    expect.objectContaining({
      message: "Pineapples are not in stock",
    })
  );
});
```

### 비동기 함수 테스트 예시

```ts
function getAsyncFruitStock() {
  return Promise.reject(new Error("empty"));
}

test("throws on async error", async () => {
  await expect(() => getAsyncFruitStock()).rejects.toThrowError("empty");
});
```

## toMatchSnapshot

- **타입:** `<T>(shape?: Partial<T> | string, hint?: string) => void`

값이 최신 스냅샷과 **일치하는지** 확인합니다.\
옵션으로 `hint` 문자열을 제공하면 테스트 이름에 추가되어 `.snap` 파일 안에서 스냅샷을 구분하는 데 도움을 줍니다.

```ts
import { expect, test } from "vitest";

test("matches snapshot", () => {
  const data = { foo: new Set(["bar", "snapshot"]) };
  expect(data).toMatchSnapshot();
});
```

값 전체 대신 **객체의 형태(shape)**만 검사할 수도 있습니다:

```ts
test("matches snapshot with shape", () => {
  const data = { foo: new Set(["bar", "snapshot"]) };
  expect(data).toMatchSnapshot({ foo: expect.any(Set) });
});
```

> 💡 스냅샷이 불일치할 경우:
>
> - `u` 키를 눌러 한 번만 업데이트하거나
> - CLI에서 `-u`, `--update` 플래그를 사용해 자동 업데이트할 수 있습니다.

## toMatchInlineSnapshot

- **타입:** `<T>(shape?: Partial<T> | string, snapshot?: string, hint?: string) => void`

값이 인라인 스냅샷과 **일치하는지** 확인합니다.\
Vitest는 스냅샷 문자열을 `.snap` 파일이 아닌 **테스트 파일 내부**에 삽입하고 업데이트합니다.

```ts
import { expect, test } from "vitest";

test("matches inline snapshot", () => {
  const data = { foo: new Set(["bar", "snapshot"]) };
  expect(data).toMatchInlineSnapshot(`
    {
      "foo": Set {
        "bar",
        "snapshot",
      },
    }
  `);
});
```

객체의 구조만 테스트할 수도 있습니다:

```ts
test("matches inline snapshot with shape", () => {
  const data = { foo: new Set(["bar", "snapshot"]) };
  expect(data).toMatchInlineSnapshot(
    { foo: expect.any(Set) },
    `
    {
      "foo": Any<Set>,
    }
  `
  );
});
```

## toHaveBeenCalled

- **타입:** `() => Awaitable<void>`

이 어서션은 **함수가 호출된 적이 있는지** 확인합니다.\
테스트 대상 함수는 반드시 **스파이 함수(spy)**여야 합니다.

```ts
import { expect, test, vi } from "vitest";

const market = {
  buy(subject: string, amount: number) {
    // ...
  },
};

test("spy function", () => {
  const buySpy = vi.spyOn(market, "buy");

  expect(buySpy).not.toHaveBeenCalled();

  market.buy("apples", 10);

  expect(buySpy).toHaveBeenCalled();
});
```

## toHaveBeenCalledTimes

- **타입:** `(amount: number) => Awaitable<void>`

이 어서션은 함수가 **지정한 횟수만큼 호출되었는지** 확인합니다.\
테스트 대상은 반드시 **스파이 함수**여야 합니다.

```ts
import { expect, test, vi } from "vitest";

const market = {
  buy(subject: string, amount: number) {
    // ...
  },
};

test("spy function called two times", () => {
  const buySpy = vi.spyOn(market, "buy");

  market.buy("apples", 10);
  market.buy("apples", 20);

  expect(buySpy).toHaveBeenCalledTimes(2);
});
```

## toHaveBeenCalledWith

- **타입:** `(...args: any[]) => Awaitable<void>`

이 어서션은 함수가 **특정 인자와 함께 호출된 적이 있는지** 확인합니다.\
단 한 번이라도 해당 인자들과 함께 호출되었다면 통과합니다.\
역시 **스파이 함수**여야 합니다.

```ts
import { expect, test, vi } from "vitest";

const market = {
  buy(subject: string, amount: number) {
    // ...
  },
};

test("spy function", () => {
  const buySpy = vi.spyOn(market, "buy");

  market.buy("apples", 10);
  market.buy("apples", 20);

  expect(buySpy).toHaveBeenCalledWith("apples", 10);
  expect(buySpy).toHaveBeenCalledWith("apples", 20);
});
```

## toHaveBeenCalledBefore

- **타입:** `(mock: MockInstance, failIfNoFirstInvocation?: boolean) => Awaitable<void>`

이 어서션은 **첫 번째 Mock 함수가 두 번째 Mock 함수보다 먼저 호출되었는지** 확인합니다.

```ts
test("calls mock1 before mock2", () => {
  const mock1 = vi.fn();
  const mock2 = vi.fn();

  mock1();
  mock2();
  mock1();

  expect(mock1).toHaveBeenCalledBefore(mock2);
});
```

## toHaveBeenCalledAfter

- **타입:** `(mock: MockInstance, failIfNoFirstInvocation?: boolean) => Awaitable<void>`

이 어서션은 **첫 번째 Mock 함수가 두 번째 Mock 함수보다 나중에 호출되었는지** 확인합니다.

```ts
test("calls mock1 after mock2", () => {
  const mock1 = vi.fn();
  const mock2 = vi.fn();

  mock2();
  mock1();
  mock2();

  expect(mock1).toHaveBeenCalledAfter(mock2);
});
```

## toHaveBeenCalledExactlyOnceWith

- **타입:** `(...args: any[]) => Awaitable<void>`

이 어서션은 함수가 **정확히 한 번만 호출되었고**, 그 호출이 **특정 인자들과 함께 이루어졌는지** 확인합니다.\
테스트 대상은 **스파이 함수**여야 합니다.

```ts
import { expect, test, vi } from "vitest";

const market = {
  buy(subject: string, amount: number) {
    // ...
  },
};

test("spy function", () => {
  const buySpy = vi.spyOn(market, "buy");

  market.buy("apples", 10);

  expect(buySpy).toHaveBeenCalledExactlyOnceWith("apples", 10);
});
```

## toHaveBeenLastCalledWith

- **타입:** `(...args: any[]) => Awaitable<void>`

이 어서션은 함수가 **마지막으로 호출되었을 때 특정 인자들로 호출되었는지** 확인합니다.\
테스트 대상은 **스파이 함수**여야 합니다.

```ts
import { expect, test, vi } from "vitest";

const market = {
  buy(subject: string, amount: number) {
    // ...
  },
};

test("spy function", () => {
  const buySpy = vi.spyOn(market, "buy");

  market.buy("apples", 10);
  market.buy("apples", 20);

  expect(buySpy).not.toHaveBeenLastCalledWith("apples", 10);
  expect(buySpy).toHaveBeenLastCalledWith("apples", 20);
});
```

## toHaveBeenNthCalledWith

- **타입:** `(time: number, ...args: any[]) => Awaitable<void>`

이 어서션은 함수가 **n번째 호출**에서 특정 인자들과 함께 호출되었는지 확인합니다.\
인덱스는 1부터 시작합니다.\
테스트 대상은 **스파이 함수**여야 합니다.

```ts
import { expect, test, vi } from "vitest";

const market = {
  buy(subject: string, amount: number) {
    // ...
  },
};

test("first call of spy function called with right params", () => {
  const buySpy = vi.spyOn(market, "buy");

  market.buy("apples", 10);
  market.buy("apples", 20);

  expect(buySpy).toHaveBeenNthCalledWith(1, "apples", 10);
});
```

## toHaveReturned

- **타입:** `() => Awaitable<void>`

이 어서션은 함수가 **정상적으로 값을 반환한 적이 있는지** 확인합니다.\
즉, 예외를 던지지 않고 `return`한 적이 한 번이라도 있어야 합니다.\
테스트 대상은 **스파이 함수**여야 합니다.

```ts
import { expect, test, vi } from "vitest";

function getApplesPrice(amount: number) {
  const PRICE = 10;
  return amount * PRICE;
}

test("spy function returned a value", () => {
  const getPriceSpy = vi.fn(getApplesPrice);

  const price = getPriceSpy(10);

  expect(price).toBe(100);
  expect(getPriceSpy).toHaveReturned();
});
```

## toHaveReturnedTimes

- **타입:** `(amount: number) => Awaitable<void>`

이 어서션은 함수가 **정상적으로 값을 반환한 횟수**를 확인합니다.\
예외 없이 return한 경우만 포함되며, 스파이 함수여야 합니다.

```ts
import { expect, test, vi } from "vitest";

test("spy function returns a value two times", () => {
  const sell = vi.fn((product: string) => ({ product }));

  sell("apples");
  sell("bananas");

  expect(sell).toHaveReturnedTimes(2);
});
```

## toHaveReturnedWith

- **타입:** `(returnValue: any) => Awaitable<void>`

이 어서션은 함수가 한 번 이상 **지정된 값을 반환했는지** 확인합니다.\
테스트 대상은 **스파이 함수**여야 합니다.

```ts
import { expect, test, vi } from "vitest";

test("spy function returns a product", () => {
  const sell = vi.fn((product: string) => ({ product }));

  sell("apples");

  expect(sell).toHaveReturnedWith({ product: "apples" });
});
```

## toHaveLastReturnedWith

- **타입:** `(returnValue: any) => Awaitable<void>`

이 어서션은 함수가 **마지막 호출에서 특정 값을 반환했는지** 확인합니다.\
테스트 대상은 **스파이 함수**여야 합니다.

```ts
import { expect, test, vi } from "vitest";

test("spy function returns bananas on a last call", () => {
  const sell = vi.fn((product: string) => ({ product }));

  sell("apples");
  sell("bananas");

  expect(sell).toHaveLastReturnedWith({ product: "bananas" });
});
```

## toHaveNthReturnedWith

- **타입:** `(time: number, returnValue: any) => Awaitable<void>`

이 어서션은 함수가 **n번째 호출에서 특정 값을 반환했는지** 확인합니다.\
인덱스는 1부터 시작합니다.\
테스트 대상은 **스파이 함수**여야 합니다.

```ts
import { expect, test, vi } from "vitest";

test("spy function returns bananas on second call", () => {
  const sell = vi.fn((product: string) => ({ product }));

  sell("apples");
  sell("bananas");

  expect(sell).toHaveNthReturnedWith(2, { product: "bananas" });
});
```

## toHaveResolved

- **타입:** `() => Awaitable<void>`

이 어서션은 함수가 최소 한 번 **프로미스를 정상적으로 resolve했는지** 확인합니다.\
reject된 경우는 포함되지 않습니다.

```ts
import { expect, test, vi } from "vitest";
import db from "./db/apples.js";

async function getApplesPrice(amount: number) {
  return amount * (await db.get("price"));
}

test("spy function resolved a value", async () => {
  const getPriceSpy = vi.fn(getApplesPrice);

  const price = await getPriceSpy(10);

  expect(price).toBe(100);
  expect(getPriceSpy).toHaveResolved();
});
```

## toHaveResolvedTimes

- **타입:** `(amount: number) => Awaitable<void>`

이 어서션은 함수가 **정상적으로 resolve된 횟수**를 확인합니다.\
아직 resolve되지 않았거나, reject된 경우는 포함되지 않습니다.

```ts
import { expect, test, vi } from "vitest";

test("spy function resolved a value two times", async () => {
  const sell = vi.fn((product: string) => Promise.resolve({ product }));

  await sell("apples");
  await sell("bananas");

  expect(sell).toHaveResolvedTimes(2);
});
```

## toHaveResolvedWith

- **타입:** `(returnValue: any) => Awaitable<void>`

이 어서션은 함수가 **resolve한 값이 특정 값과 일치했는지** 확인합니다.

```ts
import { expect, test, vi } from "vitest";

test("spy function resolved a product", async () => {
  const sell = vi.fn((product: string) => Promise.resolve({ product }));

  await sell("apples");

  expect(sell).toHaveResolvedWith({ product: "apples" });
});
```

## toHaveLastResolvedWith

- **타입:** `(returnValue: any) => Awaitable<void>`

이 어서션은 함수가 **마지막 resolve에서 특정 값을 반환했는지** 확인합니다.

```ts
import { expect, test, vi } from "vitest";

test("spy function resolves bananas on a last call", async () => {
  const sell = vi.fn((product: string) => Promise.resolve({ product }));

  await sell("apples");
  await sell("bananas");

  expect(sell).toHaveLastResolvedWith({ product: "bananas" });
});
```

## toHaveNthResolvedWith

- **타입:** `(time: number, returnValue: any) => Awaitable<void>`

이 어서션은 함수가 **n번째 호출에서 특정 값을 resolve했는지** 확인합니다.

```ts
import { expect, test, vi } from "vitest";

test("spy function returns bananas on second call", async () => {
  const sell = vi.fn((product: string) => Promise.resolve({ product }));

  await sell("apples");
  await sell("bananas");

  expect(sell).toHaveNthResolvedWith(2, { product: "bananas" });
});
```

## toSatisfy

- **타입:** `(predicate: (value: any) => boolean) => Awaitable<void>`

`toSatisfy`는 값이 **주어진 조건(predicate)**을 만족하는지 확인합니다.

```ts
import { describe, expect, it } from "vitest";

const isOdd = (value: number) => value % 2 !== 0;

describe("toSatisfy()", () => {
  it("pass with 1", () => {
    expect(1).toSatisfy(isOdd);
  });

  it("pass with negation", () => {
    expect(2).not.toSatisfy(isOdd);
  });
});
```

## resolves

- **타입:** `Promisify<Assertions>`

`resolves`는 **비동기 코드의 어서션을 간결하게 작성**할 수 있도록 도와줍니다.\
프로미스를 해제(unwrapping)하고 그 결과값을 일반 어서션으로 확인합니다.\
프로미스가 reject되면 테스트는 실패합니다.

```ts
import { expect, test } from "vitest";

async function buyApples() {
  return fetch("/buy/apples").then((r) => r.json());
}

test("buyApples returns new stock id", async () => {
  await expect(buyApples()).resolves.toEqual({ id: 1 }); // Jest 스타일
  await expect(buyApples()).resolves.to.equal({ id: 1 }); // Chai 스타일
});
```

> ⚠️ `await`하지 않으면 어서션이 호출되지 않아 테스트가 항상 통과하는 **거짓 양성(false positive)** 문제가 생깁니다.\
> 이를 방지하려면 `expect.assertions(number)`를 함께 사용하거나 `await`를 반드시 붙이세요.\
> Vitest 3: `await` 누락 시 경고 출력  
> Vitest 4: `await` 누락 시 테스트 실패 처리

## rejects

- **타입:** `Promisify<Assertions>`

`rejects`는 **프로미스가 reject되는 이유를 확인**할 수 있도록 도와줍니다.\
프로미스가 성공적으로 resolve되면 어서션은 실패합니다.

```ts
import { expect, test } from "vitest";

async function buyApples(id) {
  if (!id) {
    throw new Error("no id");
  }
}

test("buyApples throws an error when no id provided", async () => {
  await expect(buyApples()).rejects.toThrow("no id");
});
```

> ⚠️ `await`하지 않으면 마찬가지로 테스트가 항상 통과합니다.\
> Vitest 3: 경고  
> Vitest 4: 실패로 처리

## expect.assertions

- **타입:** `(count: number) => void`

테스트가 끝났을 때 **정확히 몇 개의 어서션이 호출되었는지** 확인합니다.\
특히 **비동기 코드**에서 어서션이 호출되었는지 보장할 때 유용합니다.

```ts
import { expect, test } from "vitest";

async function doAsync(...cbs) {
  await Promise.all(cbs.map((cb, index) => cb({ index })));
}

test("all assertions are called", async () => {
  expect.assertions(2);

  function callback1(data) {
    expect(data).toBeTruthy();
  }
  function callback2(data) {
    expect(data).toBeTruthy();
  }

  await doAsync(callback1, callback2);
});
```

> ⚠️ `test.concurrent`와 함께 사용할 때는 test context의 `expect`를 사용해야 정확하게 동작합니다.

## expect.hasAssertions

- **타입:** `() => void`

테스트가 끝났을 때 **최소한 하나의 어서션이 호출되었는지** 확인합니다.\
비동기 콜백 내에서 어서션이 호출되는 경우에 유용합니다.

```ts
import { expect, test } from "vitest";
import { db } from "./db.js";

const cbs = [];

function onSelect(cb) {
  cbs.push(cb);
}

function select(id) {
  return db.select({ id }).then((data) => {
    return Promise.all(cbs.map((cb) => cb(data)));
  });
}

test("callback was called", async () => {
  expect.hasAssertions();

  onSelect((data) => {
    expect(data).toBeTruthy();
  });

  await select(3);
});
```

## expect.unreachable

- **타입:** `(message?: string) => never`

이 메서드는 **절대로 실행되어선 안 되는 코드에 도달했을 때** 사용합니다.\
에러 처리를 강제하거나, switch 문에서 모든 case를 다뤘는지 확인할 때 유용합니다.

```ts
import { expect, test } from "vitest";

async function build(dir) {
  if (dir.includes("no-src")) {
    throw new Error(`${dir}/src does not exist`);
  }
}

const errorDirs = [
  "no-src-folder",
  // ...
];

test.each(errorDirs)('build fails with "%s"', async (dir) => {
  try {
    await build(dir);
    expect.unreachable("Should not pass build");
  } catch (err: any) {
    expect(err).toBeInstanceOf(Error);
    expect(err.stack).toContain("build");

    switch (dir) {
      case "no-src-folder":
        expect(err.message).toBe(`${dir}/src does not exist`);
        break;
      default:
        expect.unreachable("All error test must be handled");
        break;
    }
  }
});
```

## expect.anything

- **타입:** `() => any`

비대칭 매처(asymmetric matcher)로, 값이 **`null` 또는 `undefined`가 아니기만 하면** 항상 true를 반환합니다.\
속성의 존재만 확인하고 값에는 관심이 없을 때 유용합니다.

```ts
import { expect, test } from "vitest";

test('object has "apples" key', () => {
  expect({ apples: 22 }).toEqual({ apples: expect.anything() });
});
```

## expect.any

- **타입:** `(constructor: unknown) => any`

비대칭 매처로, 값이 **지정한 생성자(constructor)의 인스턴스**인지 확인합니다.\
예를 들어, 매번 새로 생성되는 값의 타입만 검사하고자 할 때 유용합니다.

```ts
import { expect, test } from "vitest";
import { generateId } from "./generators.js";

test('"id" is a number', () => {
  expect({ id: generateId() }).toEqual({ id: expect.any(Number) });
});
```

## expect.closeTo {#expect-closeto}

- **타입:** `(expected: any, precision?: number) => any`

객체 속성이나 배열 요소에서 **부동소수점 값을 비교**할 때 사용합니다.\
숫자 자체를 비교할 때는 `.toBeCloseTo`를 사용하세요.\
`precision`은 소수점 이하 비교 자릿수이며 기본값은 2입니다.

```ts
test("compare float in object properties", () => {
  expect({
    title: "0.1 + 0.2",
    sum: 0.1 + 0.2,
  }).toEqual({
    title: "0.1 + 0.2",
    sum: expect.closeTo(0.3, 5),
  });
});
```

## expect.arrayContaining

- **타입:** `<T>(expected: T[]) => any`

값이 배열이고, 특정 요소들을 **하나 이상 포함하고 있는지** 확인하는 비대칭 매처입니다.

```ts
import { expect, test } from "vitest";

test("basket includes fuji", () => {
  const basket = {
    varieties: ["Empire", "Fuji", "Gala"],
    count: 3,
  };
  expect(basket).toEqual({
    count: 3,
    varieties: expect.arrayContaining(["Fuji"]),
  });
});
```

> 💡 `expect.not`과 함께 사용하여 특정 요소가 포함되지 않았음을 확인할 수도 있습니다.

## expect.objectContaining

- **타입:** `(expected: any) => any`

객체가 특정 속성을 **포함하고 있는지** 확인하는 비대칭 매처입니다.

```ts
import { expect, test } from "vitest";

test("basket has empire apples", () => {
  const basket = {
    varieties: [
      {
        name: "Empire",
        count: 1,
      },
    ],
  };
  expect(basket).toEqual({
    varieties: [expect.objectContaining({ name: "Empire" })],
  });
});
```

## expect.stringContaining

- **타입:** `(expected: any) => any`

문자열이 특정 **부분 문자열**을 포함하고 있는지를 확인하는 비대칭 매처입니다.

```ts
import { expect, test } from "vitest";

test('variety has "Emp" in its name', () => {
  const variety = {
    name: "Empire",
    count: 1,
  };
  expect(variety).toEqual({
    name: expect.stringContaining("Emp"),
    count: 1,
  });
});
```

## expect.stringMatching

- **타입:** `(expected: any) => any`

문자열이 특정 **정규식**이나 문자열과 일치하는지를 확인하는 비대칭 매처입니다.

```ts
import { expect, test } from "vitest";

test('variety ends with "re"', () => {
  const variety = {
    name: "Empire",
    count: 1,
  };
  expect(variety).toEqual({
    name: expect.stringMatching(/re$/),
    count: 1,
  });
});
```

## expect.extend

- **타입:** `(matchers: MatchersObject) => void`

기본 매처를 확장하거나, 새로운 매처를 추가할 수 있습니다.\
이 함수는 비대칭 매처도 자동으로 생성해줍니다.

```ts
import { expect, test } from "vitest";

test("custom matchers", () => {
  expect.extend({
    toBeFoo: (received, expected) => {
      if (received !== "foo") {
        return {
          message: () => `expected ${received} to be foo`,
          pass: false,
        };
      }
      return {
        message: () => `received is foo`,
        pass: true,
      };
    },
  });

  expect("foo").toBeFoo();
  expect({ foo: "foo" }).toEqual({ foo: expect.toBeFoo() });
});
```

> 💡 전역 매처로 등록하고 싶다면 `setupFiles`에서 이 함수를 호출하세요.\
> 이 함수는 Jest의 `expect.extend`와 호환되므로 관련 라이브러리도 사용할 수 있습니다.

### 타입스크립트 사용자용 매처 확장

Vitest 0.31.0 이후부터는 타입 선언을 통해 커스텀 매처를 확장할 수 있습니다.\
`vitest.d.ts` 등의 선언 파일에 다음을 추가하세요:

```ts
interface CustomMatchers<R = unknown> {
  toBeFoo: () => R;
}

declare module "vitest" {
  interface Assertion<T = any> extends CustomMatchers<T> {}
  interface AsymmetricMatchersContaining extends CustomMatchers {}
}
```

> ⚠️ `tsconfig.json`에 이 선언 파일이 포함되도록 해야 합니다.

## expect.addSnapshotSerializer

- **타입:** `(plugin: PrettyFormatPlugin) => void`

스냅샷 생성 시 사용할 **커스텀 직렬화(serializer)**를 추가합니다.\
이는 고급 기능이며, 자세한 내용은 [스냅샷 가이드](/guide/snapshot#custom-serializer)를 참고하세요.

```ts
expect.addSnapshotSerializer(myCustomPlugin);
```

> 💡 글로벌 설정을 위해서는 [`setupFiles`](/config/#setupfiles) 내부에서 호출하세요.\
> 이 설정은 모든 테스트의 스냅샷에 영향을 미칩니다.
> 💡 Vue CLI에서 Jest를 사용했던 경험이 있다면 [`jest-serializer-vue`](https://www.npmjs.com/package/jest-serializer-vue) 패키지를 설치하는 것이 좋습니다.\
> 그렇지 않으면 스냅샷이 문자열로 감싸지고 `"`가 escape 처리되어 출력될 수 있습니다.

## expect.addEqualityTesters {#expect-addequalitytesters}

- **타입:** `(tester: Array<Tester>) => void`

사용자 정의 **동등성 검사 함수(tester)**를 등록할 수 있습니다.\
이 함수들은 객체 비교를 수행할 때 matcher 내부에서 사용됩니다.\
Jest의 `expect.addEqualityTesters`와 호환됩니다.

### 예시: 애너그램 비교기

```ts
import { expect, test } from "vitest";

class AnagramComparator {
  public word: string;

  constructor(word: string) {
    this.word = word;
  }

  equals(other: AnagramComparator): boolean {
    const cleanStr1 = this.word.replace(/ /g, "").toLowerCase();
    const cleanStr2 = other.word.replace(/ /g, "").toLowerCase();

    const sortedStr1 = cleanStr1.split("").sort().join("");
    const sortedStr2 = cleanStr2.split("").sort().join("");

    return sortedStr1 === sortedStr2;
  }
}

function isAnagramComparator(a: unknown): a is AnagramComparator {
  return a instanceof AnagramComparator;
}

function areAnagramsEqual(a: unknown, b: unknown): boolean | undefined {
  const isA = isAnagramComparator(a);
  const isB = isAnagramComparator(b);

  if (isA && isB) {
    return a.equals(b);
  } else if (isA === isB) {
    return undefined; // 비교할 수 없음
  } else {
    return false; // 한 쪽만 AnagramComparator
  }
}

expect.addEqualityTesters([areAnagramsEqual]);

test("custom equality tester", () => {
  expect(new AnagramComparator("listen")).toEqual(
    new AnagramComparator("silent")
  );
});
```
