# assertType

::: warning
런타임에서는 이 함수가 **아무 동작도 하지 않습니다**.  
[타입 검사 기능을 활성화하려면](/guide/testing-types#run-typechecking), `--typecheck` 플래그를 반드시 전달해야 합니다.
:::

- **타입:** `<T>(value: T): void`

이 함수는 [`expectTypeOf`](/api/expect-typeof)의 **대안**으로 사용할 수 있으며,  
전달된 인자의 타입이 제네릭으로 지정한 타입과 **동일한지** 쉽게 검증할 수 있습니다.

```ts
import { assertType } from "vitest";

function concat(a: string, b: string): string;
function concat(a: number, b: number): number;
function concat(a: string | number, b: string | number): string | number;

assertType<string>(concat("a", "b")); // 문자열 반환
assertType<number>(concat(1, 2)); // 숫자 반환

// @ts-expect-error: 타입이 일치하지 않음 → 오류 발생 예상
assertType(concat("a", 2));
```
