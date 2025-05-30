# expectTypeOf

::: warning
런타임 중에는 이 함수가 아무 동작도 하지 않습니다. [타입 검사 활성화](/guide/testing-types#run-typechecking)를 위해 `--typecheck` 플래그를 반드시 전달해야 합니다.
:::

- **타입:** `<T>(a: unknown) => ExpectTypeOf`

## not

- **타입:** `ExpectTypeOf`

`.not` 속성을 사용하여 모든 어서션을 부정할 수 있습니다.

## toEqualTypeOf

- **타입:** `<T>(expected: T) => void`

이 매처는 타입이 완전히 동일한지를 검사합니다. 값이 다르더라도 타입이 같다면 실패하지 않습니다. 단, 객체에 속성이 빠져 있다면 실패합니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf({ a: 1 }).toEqualTypeOf<{ a: number }>();
expectTypeOf({ a: 1 }).toEqualTypeOf({ a: 1 });
expectTypeOf({ a: 1 }).toEqualTypeOf({ a: 2 });
expectTypeOf({ a: 1, b: 1 }).not.toEqualTypeOf<{ a: number }>();
```

## toMatchTypeOf

- **타입:** `<T>(expected: T) => void`

이 매처는 기대하는 타입을 `확장`하는지 확인합니다. `toEqual`과는 다르며, [expect의](/api/expect) `toMatchObject()`와 유사합니다. 객체가 주어진 타입과 "일치하는지"를 확인할 수 있습니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf({ a: 1, b: 1 }).toMatchTypeOf({ a: 1 });
expectTypeOf<number>().toMatchTypeOf<string | number>();
expectTypeOf<string | number>().not.toMatchTypeOf<number>();
```

## extract

- **타입:** `ExpectTypeOf<ExtractedUnion>`

`.extract`를 사용하여 유니언 타입에서 특정 타입을 추출해 테스트할 수 있습니다.

```ts
import { expectTypeOf } from "vitest";

type ResponsiveProp<T> = T | T[] | { xs?: T; sm?: T; md?: T };

interface CSSProperties {
  margin?: string;
  padding?: string;
}

function getResponsiveProp<T>(_props: T): ResponsiveProp<T> {
  return {};
}

const cssProperties: CSSProperties = { margin: "1px", padding: "2px" };

expectTypeOf(getResponsiveProp(cssProperties))
  .extract<{ xs?: any }>() // 유니언에서 마지막 타입 추출
  .toEqualTypeOf<{
    xs?: CSSProperties;
    sm?: CSSProperties;
    md?: CSSProperties;
  }>();

expectTypeOf(getResponsiveProp(cssProperties))
  .extract<unknown[]>() // 유니언에서 배열 타입 추출
  .toEqualTypeOf<CSSProperties[]>();
```

::: warning
유니언에서 타입을 찾을 수 없는 경우 `.extract`는 `never`을 반환합니다.
:::

## exclude

- **타입:** `ExpectTypeOf<NonExcludedUnion>`

`.exclude`를 사용하여 유니언에서 특정 타입을 제거하고 테스트할 수 있습니다.

```ts
import { expectTypeOf } from "vitest";

type ResponsiveProp<T> = T | T[] | { xs?: T; sm?: T; md?: T };

interface CSSProperties {
  margin?: string;
  padding?: string;
}

function getResponsiveProp<T>(_props: T): ResponsiveProp<T> {
  return {};
}

const cssProperties: CSSProperties = { margin: "1px", padding: "2px" };

expectTypeOf(getResponsiveProp(cssProperties))
  .exclude<unknown[]>()
  .exclude<{ xs?: unknown }>() // 또는 .exclude<unknown[] | { xs?: unknown }>()
  .toEqualTypeOf<CSSProperties>();
```

::: warning
제외할 타입을 찾을 수 없으면 `.exclude`는 `never`을 반환합니다.
:::

## returns

- **타입:** `ExpectTypeOf<ReturnValue>`

`.returns`는 함수 타입의 반환 값을 추출합니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf(() => {}).returns.toBeVoid();
expectTypeOf((a: number) => [a, a]).returns.toEqualTypeOf([1, 2]);
```

::: warning
함수가 아닌 타입에 사용하면 `never`을 반환하여 이후 매처 체인이 불가능해집니다.
:::

## parameters

- **타입:** `ExpectTypeOf<Parameters>`

`.parameters`를 사용해 함수의 인자들을 배열로 추출하고 어서션을 수행할 수 있습니다.

```ts
import { expectTypeOf } from "vitest";

type NoParam = () => void;
type HasParam = (s: string) => void;

expectTypeOf<NoParam>().parameters.toEqualTypeOf<[]>();
expectTypeOf<HasParam>().parameters.toEqualTypeOf<[string]>();
```

::: warning
함수가 아닌 타입에 사용하면 `never`을 반환하여 이후 매처 체인이 불가능해집니다.
:::

::: tip
보다 표현력 있는 어서션을 원한다면 [`.toBeCallableWith`](#tobecallablewith)을 사용할 수 있습니다.
:::

## parameter

- **타입:** `(nth: number) => ExpectTypeOf`

`.parameter(number)`를 통해 특정 인자 타입을 추출하여 테스트할 수 있습니다.

```ts
import { expectTypeOf } from "vitest";

function foo(a: number, b: string) {
  return [a, b];
}

expectTypeOf(foo).parameter(0).toBeNumber();
expectTypeOf(foo).parameter(1).toBeString();
```

::: warning
함수가 아닌 타입에 사용하면 `never`을 반환하여 이후 매처 체인이 불가능해집니다.
:::

## constructorParameters

- **타입:** `ExpectTypeOf<ConstructorParameters>`

`.constructorParameters`는 생성자의 인자를 배열로 추출하여 어서션을 수행할 수 있습니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf(Date).constructorParameters.toEqualTypeOf<
  [] | [string | number | Date]
>();
```

::: warning
함수가 아닌 타입에 사용하면 `never`을 반환하여 이후 매처 체인이 불가능해집니다.
:::

::: tip
보다 표현력 있는 어서션을 원한다면 [`.toBeConstructibleWith`](#tobeconstructiblewith)을 사용할 수 있습니다.
:::

## instance

- **타입:** `ExpectTypeOf<ConstructableInstance>`

클래스의 인스턴스에 대해 어서션을 수행할 수 있습니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf(Date).instance.toHaveProperty("toISOString");
```

::: warning
함수가 아닌 타입에 사용하면 `never`을 반환하여 이후 매처 체인이 불가능해집니다.
:::

## items

- **타입:** `ExpectTypeOf<T>`

`.items`를 사용하면 배열의 요소 타입을 추출하여 어서션을 수행할 수 있습니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf([1, 2, 3]).items.toEqualTypeOf<number>();
expectTypeOf([1, 2, 3]).items.not.toEqualTypeOf<string>();
```

## resolves

- **타입:** `ExpectTypeOf<ResolvedPromise>`

`.resolves`는 `Promise`의 결과 타입을 추출합니다.

```ts
import { expectTypeOf } from "vitest";

async function asyncFunc() {
  return 123;
}

expectTypeOf(asyncFunc).returns.resolves.toBeNumber();
expectTypeOf(Promise.resolve("string")).resolves.toBeString();
```

::: warning
`Promise`가 아닌 타입에 사용하면 `never`을 반환하여 이후 매처 체인이 불가능해집니다.
:::

## guards

- **타입:** `ExpectTypeOf<Guard>`

타입 가드(`v is T`)에서 `T`를 추출해 테스트할 수 있습니다.

```ts
import { expectTypeOf } from "vitest";

function isString(v: any): v is string {
  return typeof v === "string";
}
expectTypeOf(isString).guards.toBeString();
```

::: warning
가드 함수가 아닌 경우 `never`을 반환하여 이후 체인 불가능.
:::

## asserts

- **타입:** `ExpectTypeOf<Assert>`

`asserts v is T` 함수에서 `T`를 추출해 테스트할 수 있습니다.

```ts
import { expectTypeOf } from "vitest";

function assertNumber(v: any): asserts v is number {
  if (typeof v !== "number") {
    throw new TypeError("Nope !");
  }
}

expectTypeOf(assertNumber).asserts.toBeNumber();
```

::: warning
assert 함수가 아닌 경우 `never`을 반환하여 이후 체인 불가능.
:::

## toBeAny

- **타입:** `() => void`

주어진 타입이 `any`인지 검사합니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf<any>().toBeAny();
expectTypeOf({} as any).toBeAny();
expectTypeOf("string").not.toBeAny();
```

## toBeUnknown

- **타입:** `() => void`

주어진 타입이 `unknown`인지 검사합니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf().toBeUnknown();
expectTypeOf({} as unknown).toBeUnknown();
expectTypeOf("string").not.toBeUnknown();
```

## toBeNever

- **타입:** `() => void`

주어진 타입이 `never`인지 검사합니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf<never>().toBeNever();
expectTypeOf((): never => {}).returns.toBeNever();
```

## toBeFunction

- **타입:** `() => void`

주어진 타입이 함수인지 검사합니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf(42).not.toBeFunction();
expectTypeOf((): never => {}).toBeFunction();
```

## toBeObject

- **타입:** `() => void`

주어진 타입이 객체인지 검사합니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf(42).not.toBeObject();
expectTypeOf({}).toBeObject();
```

## toBeArray

- **타입:** `() => void`

주어진 타입이 `Array<T>`인지 검사합니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf(42).not.toBeArray();
expectTypeOf([]).toBeArray();
expectTypeOf([1, 2]).toBeArray();
expectTypeOf([{}, 42]).toBeArray();
```

## toBeString

- **타입:** `() => void`

주어진 타입이 문자열인지 검사합니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf(42).not.toBeString();
expectTypeOf("").toBeString();
expectTypeOf("a").toBeString();
```

## toBeBoolean

- **타입:** `() => void`

주어진 타입이 boolean인지 검사합니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf(42).not.toBeBoolean();
expectTypeOf(true).toBeBoolean();
expectTypeOf<boolean>().toBeBoolean();
```

## toBeVoid

- **타입:** `() => void`

주어진 타입이 `void`인지 검사합니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf(() => {}).returns.toBeVoid();
expectTypeOf<void>().toBeVoid();
```

## toBeSymbol

- **타입:** `() => void`

주어진 타입이 `symbol`인지 검사합니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf(Symbol(1)).toBeSymbol();
expectTypeOf<symbol>().toBeSymbol();
```

## toBeNull

- **타입:** `() => void`

주어진 타입이 `null`인지 검사합니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf(null).toBeNull();
expectTypeOf<null>().toBeNull();
expectTypeOf(undefined).not.toBeNull();
```

## toBeUndefined

- **타입:** `() => void`

주어진 타입이 `undefined`인지 검사합니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf(undefined).toBeUndefined();
expectTypeOf<undefined>().toBeUndefined();
expectTypeOf(null).not.toBeUndefined();
```

## toBeNullable

- **타입:** `() => void`

주어진 타입이 `null` 또는 `undefined`를 포함하는지 검사합니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf<undefined | 1>().toBeNullable();
expectTypeOf<null | 1>().toBeNullable();
expectTypeOf<undefined | null | 1>().toBeNullable();
```

## toBeCallableWith

- **타입:** `() => void`

주어진 함수가 특정 파라미터로 호출 가능한지 검사합니다.

```ts
import { expectTypeOf } from "vitest";

type NoParam = () => void;
type HasParam = (s: string) => void;

expectTypeOf<NoParam>().toBeCallableWith();
expectTypeOf<HasParam>().toBeCallableWith("some string");
```

::: warning
함수가 아닌 타입에 사용하면 `never`을 반환하여 이후 매처 체인이 불가능해집니다.
:::

## toBeConstructibleWith

- **타입:** `() => void`

주어진 생성자가 특정 파라미터로 인스턴스를 생성할 수 있는지 검사합니다.

```ts
import { expectTypeOf } from "vitest";

expectTypeOf(Date).toBeConstructibleWith(new Date());
expectTypeOf(Date).toBeConstructibleWith("01-01-2000");
```

::: warning
함수가 아닌 타입에 사용하면 `never`을 반환하여 이후 매처 체인이 불가능해집니다.
:::

## toHaveProperty

- **타입:** `<K extends keyof T>(property: K) => ExpectTypeOf<T[K]>`

객체에 특정 속성이 존재하는지 검사합니다. 존재하면 해당 속성 타입에 대한 매처 체인이 가능합니다.

```ts
import { expectTypeOf } from "vitest";

const obj = { a: 1, b: "" };

expectTypeOf(obj).toHaveProperty("a");
expectTypeOf(obj).not.toHaveProperty("c");

expectTypeOf(obj).toHaveProperty("a").toBeNumber();
expectTypeOf(obj).toHaveProperty("b").toBeString();
expectTypeOf(obj).toHaveProperty("a").not.toBeString();
```
