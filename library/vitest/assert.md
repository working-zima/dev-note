# assert

Vitest는 [`chai`](https://www.chaijs.com/api/assert/)로부터 `assert` 메서드를 재내보내어(재익스포트) 사용자의 조건 검증에 활용할 수 있도록 합니다.

- **타입:** `(expression: any, message?: string) => asserts expression`

`expression`이 참(truthy)인지 확인하며, 그렇지 않으면 테스트가 실패합니다.

```ts
import { assert, test } from "vitest";

test("assert", () => {
  assert("foo" !== "bar", "foo는 bar와 같지 않아야 합니다.");
});
```

## fail

- **타입:**
  - `(message?: string) => never`
  - `<T>(actual: T, expected: T, message?: string, operator?: string) => never`

강제로 테스트를 실패하게 만듭니다.

```ts
import { assert, test } from "vitest";

test("assert.fail", () => {
  assert.fail("에러 메시지");
  assert.fail("foo", "bar", "foo는 bar가 아닙니다.", "===");
});
```

## isOk

- **타입:** `<T>(value: T, message?: string) => void`
- **별칭:** `ok`

값이 참(truthy)인지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.isOk", () => {
  assert.isOk("foo", "truthy 값은 OK입니다.");
  assert.isOk(false, "false는 truthy가 아니므로 실패합니다.");
});
```

## isNotOk

- **타입:** `<T>(value: T, message?: string) => void`
- **별칭:** `notOk`

값이 거짓(falsy)인지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.isNotOk", () => {
  assert.isNotOk("foo", "truthy 값이므로 실패합니다.");
  assert.isNotOk(false, "false는 falsy이므로 통과합니다.");
});
```

## equal

- **타입:** `<T>(actual: T, expected: T, message?: string) => void`

`actual`과 `expected`가 느슨한 동등성(==)을 만족하는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.equal", () => {
  assert.equal(Math.sqrt(4), "2"); // 숫자 2와 문자열 '2'는 ==로 같음
});
```

## notEqual

- **타입:** `<T>(actual: T, expected: T, message?: string) => void`

`actual`과 `expected`가 느슨한 불일치(!=)를 만족하는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notEqual", () => {
  assert.notEqual(Math.sqrt(4), 3);
});
```

## strictEqual

- **타입:** `<T>(actual: T, expected: T, message?: string) => void`

`actual`과 `expected`가 엄격한 동등성(===)을 만족하는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.strictEqual", () => {
  assert.strictEqual(Math.sqrt(4), 2);
});
```

## deepEqual

- **타입:** `<T>(actual: T, expected: T, message?: string) => void`

`actual`과 `expected`가 깊은 동등성(deep equality)을 만족하는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.deepEqual", () => {
  assert.deepEqual({ color: "green" }, { color: "green" });
});
```

## notDeepEqual

- **타입:** `<T>(actual: T, expected: T, message?: string) => void`

`actual`과 `expected`가 깊은 동등성(deep equality)을 만족하지 않는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notDeepEqual", () => {
  assert.notDeepEqual({ color: "green" }, { color: "red" });
});
```

## isAbove

- **타입:** `(valueToCheck: number, valueToBeAbove: number, message?: string) => void`

`valueToCheck`가 `valueToBeAbove`보다 큰지(>) 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.isAbove", () => {
  assert.isAbove(5, 2, "5는 2보다 큽니다.");
});
```

## isAtLeast

- **타입:** `(valueToCheck: number, valueToBeAtLeast: number, message?: string) => void`

`valueToCheck`가 `valueToBeAtLeast`보다 크거나 같은지(>=) 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.isAtLeast", () => {
  assert.isAtLeast(5, 2, "5는 2보다 크거나 같습니다.");
  assert.isAtLeast(3, 3, "3은 3과 같습니다.");
});
```

## isBelow

- **타입:** `(valueToCheck: number, valueToBeBelow: number, message?: string) => void`

`valueToCheck`가 `valueToBeBelow`보다 작은지(<) 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.isBelow", () => {
  assert.isBelow(3, 6, "3은 6보다 작습니다.");
});
```

## isAtMost

- **타입:** `(valueToCheck: number, valueToBeAtMost: number, message?: string) => void`

`valueToCheck`가 `valueToBeAtMost`보다 작거나 같은지(<=) 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.isAtMost", () => {
  assert.isAtMost(3, 6, "3은 6보다 작거나 같습니다.");
  assert.isAtMost(4, 4, "4는 4와 같습니다.");
});
```

## isTrue

- **타입:** `<T>(value: T, message?: string) => void`

값이 `true`인지 확인합니다.

```ts
import { assert, test } from "vitest";

const testPassed = true;

test("assert.isTrue", () => {
  assert.isTrue(testPassed);
});
```

## isNotTrue

- **타입:** `<T>(value: T, message?: string) => void`

값이 `true`가 아닌지 확인합니다.

```ts
import { assert, test } from "vitest";

const testPassed = "ok";

test("assert.isNotTrue", () => {
  assert.isNotTrue(testPassed);
});
```

## isFalse

- **타입:** `<T>(value: T, message?: string) => void`

값이 `false`인지 확인합니다.

```ts
import { assert, test } from "vitest";

const testPassed = false;

test("assert.isFalse", () => {
  assert.isFalse(testPassed);
});
```

## isNotFalse

- **타입:** `<T>(value: T, message?: string) => void`

값이 `false`가 아닌지 확인합니다.

```ts
import { assert, test } from "vitest";

const testPassed = "no";

test("assert.isNotFalse", () => {
  assert.isNotFalse(testPassed);
});
```

## isNull

- **타입:** `<T>(value: T, message?: string) => void`

값이 `null`인지 확인합니다.

```ts
import { assert, test } from "vitest";

const error = null;

test("assert.isNull", () => {
  assert.isNull(error, "error는 null입니다.");
});
```

## isNotNull

- **타입:** `<T>(value: T, message?: string) => void`

값이 `null`이 아닌지 확인합니다.

```ts
import { assert, test } from "vitest";

const error = { message: "error 발생" };

test("assert.isNotNull", () => {
  assert.isNotNull(error, "error는 null이 아닌 객체입니다.");
});
```

## isNaN

- **타입:** `<T>(value: T, message?: string) => void`

값이 `NaN`인지 확인합니다.

```ts
import { assert, test } from "vitest";

const calculation = 1 * "vitest";

test("assert.isNaN", () => {
  assert.isNaN(calculation, '1 * "vitest"는 NaN입니다.');
});
```

## isNotNaN

- **타입:** `<T>(value: T, message?: string) => void`

값이 `NaN`이 아닌지 확인합니다.

```ts
import { assert, test } from "vitest";

const calculation = 1 * 2;

test("assert.isNotNaN", () => {
  assert.isNotNaN(calculation, "1 * 2는 NaN이 아닌 2입니다.");
});
```

## exists

- **타입:** `<T>(value: T, message?: string) => void`

값이 `null`도 아니고 `undefined`도 아닌지 확인합니다.

```ts
import { assert, test } from "vitest";

const name = "foo";

test("assert.exists", () => {
  assert.exists(name, "foo는 null도 undefined도 아닙니다.");
});
```

## notExists

- **타입:** `<T>(value: T, message?: string) => void`

값이 `null`이거나 `undefined`인지 확인합니다.

```ts
import { assert, test } from "vitest";

const foo = null;
const bar = undefined;

test("assert.notExists", () => {
  assert.notExists(foo, "foo는 null이므로 존재하지 않습니다.");
  assert.notExists(bar, "bar는 undefined이므로 존재하지 않습니다.");
});
```

## isUndefined

- **타입:** `<T>(value: T, message?: string) => void`

값이 `undefined`인지 확인합니다.

```ts
import { assert, test } from "vitest";

const name = undefined;

test("assert.isUndefined", () => {
  assert.isUndefined(name, "name은 undefined입니다.");
});
```

## isDefined

- **타입:** `<T>(value: T, message?: string) => void`

값이 `undefined`가 아닌지 확인합니다.

```ts
import { assert, test } from "vitest";

const name = "foo";

test("assert.isDefined", () => {
  assert.isDefined(name, "name은 undefined가 아닙니다.");
});
```

## isFunction

- **타입:** `<T>(value: T, message?: string) => void`
- **별칭:** `isCallable`

값이 함수인지 확인합니다.

```ts
import { assert, test } from "vitest";

function name() {
  return "foo";
}

test("assert.isFunction", () => {
  assert.isFunction(name, "name은 함수입니다.");
});
```

## isNotFunction

- **타입:** `<T>(value: T, message?: string) => void`
- **별칭:** `isNotCallable`

값이 함수가 아닌지 확인합니다.

```ts
import { assert, test } from "vitest";

const name = "foo";

test("assert.isNotFunction", () => {
  assert.isNotFunction(name, "name은 함수가 아닌 문자열입니다.");
});
```

## isObject

- **타입:** `<T>(value: T, message?: string) => void`

값이 `Object` 타입의 객체인지 확인합니다. (서브 클래스 객체는 해당되지 않음)

```ts
import { assert, test } from "vitest";

const someThing = { color: "red", shape: "circle" };

test("assert.isObject", () => {
  assert.isObject(someThing, "someThing은 객체입니다.");
});
```

## isNotObject

- **타입:** `<T>(value: T, message?: string) => void`

값이 `Object` 타입의 객체가 아닌지 확인합니다.

```ts
import { assert, test } from "vitest";

const someThing = "redCircle";

test("assert.isNotObject", () => {
  assert.isNotObject(someThing, "someThing은 문자열이지 객체가 아닙니다.");
});
```

## isArray

- **타입:** `<T>(value: T, message?: string) => void`

값이 배열인지 확인합니다.

```ts
import { assert, test } from "vitest";

const color = ["red", "green", "yellow"];

test("assert.isArray", () => {
  assert.isArray(color, "color는 배열입니다.");
});
```

## isNotArray

- **타입:** `<T>(value: T, message?: string) => void`

값이 배열이 아닌지 확인합니다.

```ts
import { assert, test } from "vitest";

const color = "red";

test("assert.isNotArray", () => {
  assert.isNotArray(color, "color는 문자열이며 배열이 아닙니다.");
});
```

## isString

- **타입:** `<T>(value: T, message?: string) => void`

값이 문자열인지 확인합니다.

```ts
import { assert, test } from "vitest";

const color = "red";

test("assert.isString", () => {
  assert.isString(color, "color는 문자열입니다.");
});
```

## isNotString

- **타입:** `<T>(value: T, message?: string) => void`

값이 문자열이 아닌지 확인합니다.

```ts
import { assert, test } from "vitest";

const color = ["red", "green", "yellow"];

test("assert.isNotString", () => {
  assert.isNotString(color, "color는 배열이며 문자열이 아닙니다.");
});
```

## isNumber

- **타입:** `<T>(value: T, message?: string) => void`

값이 숫자인지 확인합니다.

```ts
import { assert, test } from "vitest";

const colors = 3;

test("assert.isNumber", () => {
  assert.isNumber(colors, "colors는 숫자입니다.");
});
```

## isNotNumber

- **타입:** `<T>(value: T, message?: string) => void`

값이 숫자가 아닌지 확인합니다.

```ts
import { assert, test } from "vitest";

const colors = "3 colors";

test("assert.isNotNumber", () => {
  assert.isNotNumber(colors, "colors는 문자열이며 숫자가 아닙니다.");
});
```

## isFinite

- **타입:** `<T>(value: T, message?: string) => void`

값이 유한한 숫자인지 확인합니다. (`NaN`, `Infinity`가 아님)

```ts
import { assert, test } from "vitest";

const colors = 3;

test("assert.isFinite", () => {
  assert.isFinite(colors, "colors는 유한한 숫자입니다.");
});
```

## isBoolean

- **타입:** `<T>(value: T, message?: string) => void`

값이 불리언(boolean)인지 확인합니다.

```ts
import { assert, test } from "vitest";

const isReady = true;

test("assert.isBoolean", () => {
  assert.isBoolean(isReady, "isReady는 불리언입니다.");
});
```

## isNotBoolean

- **타입:** `<T>(value: T, message?: string) => void`

값이 불리언(boolean)이 아닌지 확인합니다.

```ts
import { assert, test } from "vitest";

const isReady = "sure";

test("assert.isNotBoolean", () => {
  assert.isNotBoolean(isReady, "isReady는 문자열이며 불리언이 아닙니다.");
});
```

## typeOf

- **타입:** `<T>(value: T, name: string, message?: string) => void`

값의 타입이 주어진 문자열 `name`과 일치하는지 확인합니다. 내부적으로 `Object.prototype.toString`을 사용합니다.

```ts
import { assert, test } from "vitest";

test("assert.typeOf", () => {
  assert.typeOf({ color: "red" }, "object", "객체입니다.");
  assert.typeOf(["red", "green"], "array", "배열입니다.");
  assert.typeOf("red", "string", "문자열입니다.");
  assert.typeOf(/red/, "regexp", "정규표현식입니다.");
  assert.typeOf(null, "null", "null입니다.");
  assert.typeOf(undefined, "undefined", "undefined입니다.");
});
```

## notTypeOf

- **타입:** `<T>(value: T, name: string, message?: string) => void`

값의 타입이 주어진 문자열 `name`과 일치하지 않는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notTypeOf", () => {
  assert.notTypeOf("red", "number", '"red"는 숫자가 아닙니다.');
});
```

## instanceOf

- **타입:** `<T>(value: T, constructor: Function, message?: string) => void`

값이 특정 생성자의 인스턴스인지 확인합니다.

```ts
import { assert, test } from "vitest";

function Person(name) {
  this.name = name;
}
const foo = new Person("foo");

class Tea {
  constructor(name) {
    this.name = name;
  }
}
const coffee = new Tea("coffee");

test("assert.instanceOf", () => {
  assert.instanceOf(foo, Person, "foo는 Person의 인스턴스입니다.");
  assert.instanceOf(coffee, Tea, "coffee는 Tea의 인스턴스입니다.");
});
```

## notInstanceOf

- **타입:** `<T>(value: T, constructor: Function, message?: string) => void`

값이 특정 생성자의 인스턴스가 아닌지 확인합니다.

```ts
import { assert, test } from "vitest";

function Person(name) {
  this.name = name;
}
const foo = new Person("foo");

class Tea {
  constructor(name) {
    this.name = name;
  }
}
const coffee = new Tea("coffee");

test("assert.notInstanceOf", () => {
  assert.notInstanceOf(foo, Tea, "foo는 Tea의 인스턴스가 아닙니다.");
});
```

## include

- **타입:**
  - `(haystack: string, needle: string, message?: string) => void`
  - `<T>(haystack: readonly T[] | ReadonlySet<T> | ReadonlyMap<any, T>, needle: T, message?: string) => void`
  - `<T extends object>(haystack: WeakSet<T>, needle: T, message?: string) => void`
  - `<T>(haystack: T, needle: Partial<T>, message?: string) => void`

문자열, 배열, 객체, Set, Map 등에서 특정 요소 또는 속성이 포함되어 있는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.include", () => {
  assert.include([1, 2, 3], 2, "배열에 값이 포함되어 있습니다.");
  assert.include("foobar", "foo", "문자열에 하위 문자열이 포함되어 있습니다.");
  assert.include(
    { foo: "bar", hello: "universe" },
    { foo: "bar" },
    "객체에 속성이 포함되어 있습니다."
  );
});
```

## notInclude

- **타입:** 위 `include`와 동일

값이 포함되어 있지 않은 경우를 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notInclude", () => {
  assert.notInclude([1, 2, 3], 4, "배열에 4는 포함되어 있지 않습니다.");
  assert.notInclude("foobar", "baz", '문자열에 "baz"는 없습니다.');
  assert.notInclude(
    { foo: "bar", hello: "universe" },
    { foo: "baz" },
    "객체에 값이 다르므로 포함되지 않습니다."
  );
});
```

## deepInclude

- **타입:**
  - `(haystack: string, needle: string, message?: string) => void`
  - `<T>(haystack: readonly T[] | ReadonlySet<T> | ReadonlyMap<any, T>, needle: T, message?: string) => void`
  - `<T>(haystack: T, needle: T extends WeakSet<any> ? never : Partial<T>, message?: string) => void`

객체나 배열에서 **깊은 동등성**으로 포함 여부를 확인합니다.

```ts
import { assert, test } from "vitest";

const obj1 = { a: 1 };
const obj2 = { b: 2 };

test("assert.deepInclude", () => {
  assert.deepInclude([obj1, obj2], { a: 1 });
  assert.deepInclude({ foo: obj1, bar: obj2 }, { foo: { a: 1 } });
});
```

## notDeepInclude

- **타입:** 위와 동일

깊은 비교 기준으로 포함되어 있지 않음을 확인합니다.

```ts
import { assert, test } from "vitest";

const obj1 = { a: 1 };
const obj2 = { b: 2 };

test("assert.notDeepInclude", () => {
  assert.notDeepInclude([obj1, obj2], { a: 10 });
  assert.notDeepInclude({ foo: obj1, bar: obj2 }, { foo: { a: 10 } });
});
```

## nestedInclude

- **타입:** `(haystack: any, needle: any, message?: string) => void`

객체가 **중첩된 속성**을 포함하고 있는지 확인합니다. 점(`.`) 및 대괄호(`[]`) 표기법을 사용하며, 속성 이름 안에 점이나 대괄호가 포함된 경우 이스케이프(`\\`)합니다.

```ts
import { assert, test } from "vitest";

test("assert.nestedInclude", () => {
  assert.nestedInclude({ ".a": { b: "x" } }, { "\\.a.[b]": "x" });
  assert.nestedInclude({ a: { "[b]": "x" } }, { "a.\\[b\\]": "x" });
});
```

## notNestedInclude

- **타입:** 동일

중첩된 속성이 포함되지 않았는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notNestedInclude", () => {
  assert.notNestedInclude({ ".a": { b: "x" } }, { "\\.a.b": "y" });
  assert.notNestedInclude({ a: { "[b]": "x" } }, { "a.\\[b\\]": "y" });
});
```

## deepNestedInclude

- **타입:** `(haystack: any, needle: any, message?: string) => void`

깊은 동등성을 이용해 중첩된 속성을 포함하는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.deepNestedInclude", () => {
  assert.deepNestedInclude({ a: { b: [{ x: 1 }] } }, { "a.b[0]": { x: 1 } });
  assert.deepNestedInclude(
    { ".a": { "[b]": { x: 1 } } },
    { "\\.a.\\[b\\]": { x: 1 } }
  );
});
```

## notDeepNestedInclude

- **타입:** 동일

깊은 비교 기준으로 중첩 속성이 포함되지 않았는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notDeepNestedInclude", () => {
  assert.notDeepNestedInclude({ a: { b: [{ x: 1 }] } }, { "a.b[0]": { y: 1 } });
  assert.notDeepNestedInclude(
    { ".a": { "[b]": { x: 1 } } },
    { "\\.a.\\[b\\]": { y: 2 } }
  );
});
```

## ownInclude

- **타입:** `(haystack: any, needle: any, message?: string) => void`

상속된 속성을 제외한 **자신의 속성만**을 기준으로 포함 여부를 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.ownInclude", () => {
  assert.ownInclude({ a: 1 }, { a: 1 });
});
```

## notOwnInclude

- **타입:** 동일

상속 속성은 무시하고, 직접 가진 속성만 비교하여 포함되지 않았는지 확인합니다.

```ts
import { assert, test } from "vitest";

const obj1 = { b: 2 };
const obj2 = Object.create(obj1);
obj2.a = 1;

test("assert.notOwnInclude", () => {
  assert.notOwnInclude(obj2, { b: 2 }); // b는 상속된 속성이므로 포함되지 않음
});
```

## deepOwnInclude

- **타입:** `(haystack: any, needle: any, message?: string) => void`

자체 속성만 대상으로 깊은 동등성 기준의 포함 여부를 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.deepOwnInclude", () => {
  assert.deepOwnInclude({ a: { b: 2 } }, { a: { b: 2 } });
});
```

## notDeepOwnInclude

- **타입:** 동일

자체 속성만 대상으로, 깊은 동등성 기준으로 **포함되지 않았는지** 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notDeepOwnInclude", () => {
  assert.notDeepOwnInclude({ a: { b: 2 } }, { a: { c: 3 } });
});
```

## property

- **타입:** `<T>(object: T, property: string, message?: string) => void`

해당 객체가 직접 또는 상속을 통해 지정한 속성을 가지고 있는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.property", () => {
  assert.property({ tea: { green: "matcha" } }, "tea");
  assert.property({ tea: { green: "matcha" } }, "toString"); // toString은 Object로부터 상속됨
});
```

## notProperty

- **타입:** 동일

객체가 지정한 속성을 갖고 있지 않은지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notProperty", () => {
  assert.notProperty({ tea: { green: "matcha" } }, "coffee");
});
```

## propertyVal

- **타입:** `<T, V>(object: T, property: string, value: V, message?: string) => void`

객체가 지정된 속성을 갖고 있으며 해당 속성의 값이 `value`와 **엄격하게 동일**한지(===) 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.propertyVal", () => {
  assert.propertyVal({ tea: "is good" }, "tea", "is good");
});
```

## notPropertyVal

- **타입:** 동일

속성의 값이 주어진 값과 **같지 않은지** 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notPropertyVal", () => {
  assert.notPropertyVal({ tea: "is good" }, "tea", "is bad");
  assert.notPropertyVal({ tea: "is good" }, "coffee", "is good");
});
```

## deepPropertyVal

- **타입:** `<T, V>(object: T, property: string, value: V, message?: string) => void`

객체가 해당 속성을 가지고 있으며, 그 값이 `value`와 **깊은 동등성**을 갖는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.deepPropertyVal", () => {
  assert.deepPropertyVal({ tea: { green: "matcha" } }, "tea", {
    green: "matcha",
  });
});
```

## notDeepPropertyVal

- **타입:** 동일

속성의 값이 주어진 값과 **깊은 동등성을 가지지 않는지** 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notDeepPropertyVal", () => {
  assert.notDeepPropertyVal({ tea: { green: "matcha" } }, "tea", {
    black: "matcha",
  });
  assert.notDeepPropertyVal({ tea: { green: "matcha" } }, "tea", {
    green: "oolong",
  });
  assert.notDeepPropertyVal({ tea: { green: "matcha" } }, "coffee", {
    green: "matcha",
  });
});
```

## nestedProperty

- **타입:** `<T>(object: T, property: string, message?: string) => void`

점(`.`) 또는 대괄호(`[]`) 표기법을 사용해 **중첩된 속성**이 존재하는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.nestedProperty", () => {
  assert.nestedProperty({ tea: { green: "matcha" } }, "tea.green");
});
```

## notNestedProperty

- **타입:** 동일

중첩된 속성이 존재하지 않는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notNestedProperty", () => {
  assert.notNestedProperty({ tea: { green: "matcha" } }, "tea.oolong");
});
```

## nestedPropertyVal

- **타입:** `<T>(object: T, property: string, value: any, message?: string) => void`

점/대괄호 표기법으로 참조된 **중첩된 속성의 값**이 주어진 값과 일치하는지 (===) 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.nestedPropertyVal", () => {
  assert.nestedPropertyVal({ tea: { green: "matcha" } }, "tea.green", "matcha");
});
```

## notNestedPropertyVal

- **타입:** 동일

중첩 속성의 값이 주어진 값과 **일치하지 않는지** 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notNestedPropertyVal", () => {
  assert.notNestedPropertyVal(
    { tea: { green: "matcha" } },
    "tea.green",
    "konacha"
  );
  assert.notNestedPropertyVal(
    { tea: { green: "matcha" } },
    "coffee.green",
    "matcha"
  );
});
```

## deepNestedPropertyVal

- **타입:** `<T>(object: T, property: string, value: any, message?: string) => void`

중첩 속성의 값이 **깊은 동등성**을 가지는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.deepNestedPropertyVal", () => {
  assert.deepNestedPropertyVal(
    { tea: { green: { matcha: "yum" } } },
    "tea.green",
    { matcha: "yum" }
  );
});
```

## notDeepNestedPropertyVal

- **타입:** 동일

중첩 속성의 값이 주어진 값과 **깊은 동등성을 가지지 않는지** 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notDeepNestedPropertyVal", () => {
  assert.notDeepNestedPropertyVal(
    { tea: { green: { matcha: "yum" } } },
    "tea.green",
    { oolong: "yum" }
  );
  assert.notDeepNestedPropertyVal(
    { tea: { green: { matcha: "yum" } } },
    "tea.green",
    { matcha: "yuck" }
  );
  assert.notDeepNestedPropertyVal(
    { tea: { green: { matcha: "yum" } } },
    "tea.black",
    { matcha: "yum" }
  );
});
```

## lengthOf

- **타입:**  
  `<T extends { readonly length?: number | undefined } | { readonly size?: number | undefined }>(object: T, length: number, message?: string) => void`

배열, 문자열, Map, Set, 객체 등에 대해 `length`나 `size`가 기대한 값과 일치하는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.lengthOf", () => {
  assert.lengthOf([1, 2, 3], 3, "배열 길이는 3입니다.");
  assert.lengthOf("foobar", 6, "문자열 길이는 6입니다.");
  assert.lengthOf(new Set([1, 2, 3]), 3, "Set의 크기는 3입니다.");
  assert.lengthOf(
    new Map([
      ["a", 1],
      ["b", 2],
      ["c", 3],
    ]),
    3,
    "Map의 크기는 3입니다."
  );
});
```

## hasAnyKeys

- **타입:**  
  `<T>(object: T, keys: Array<Object | string> | { [key: string]: any }, message?: string) => void`

객체 또는 Map/Set이 주어진 키 중 **하나 이상**을 가지고 있는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.hasAnyKeys", () => {
  assert.hasAnyKeys({ foo: 1, bar: 2, baz: 3 }, ["foo", "iDontExist", "baz"]);
  assert.hasAnyKeys(
    { foo: 1, bar: 2, baz: 3 },
    { foo: 30, iDontExist: 99, baz: 1337 }
  );
  assert.hasAnyKeys(
    new Map([
      [{ foo: 1 }, "bar"],
      ["key", "value"],
    ]),
    [{ foo: 1 }, "key"]
  );
  assert.hasAnyKeys(new Set([{ foo: "bar" }, "anotherKey"]), [
    { foo: "bar" },
    "anotherKey",
  ]);
});
```

## hasAllKeys

- **타입:**  
  `<T>(object: T, keys: Array<Object | string> | { [key: string]: any }, message?: string) => void`

객체가 주어진 키 **모두를 정확히 포함하고 있는지** 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.hasAllKeys", () => {
  assert.hasAllKeys({ foo: 1, bar: 2, baz: 3 }, ["foo", "bar", "baz"]);
  assert.hasAllKeys(
    { foo: 1, bar: 2, baz: 3 },
    { foo: 30, bar: 99, baz: 1337 }
  );
  assert.hasAllKeys(
    new Map([
      [{ foo: 1 }, "bar"],
      ["key", "value"],
    ]),
    [{ foo: 1 }, "key"]
  );
  assert.hasAllKeys(new Set([{ foo: "bar" }, "anotherKey"]), [
    { foo: "bar" },
    "anotherKey",
  ]);
});
```

## containsAllKeys

- **타입:**  
  `<T>(object: T, keys: Array<Object | string> | { [key: string]: any }, message?: string) => void`

객체가 주어진 키를 **모두 포함**하는지 확인합니다. 여분의 키가 있어도 무방합니다.

```ts
import { assert, test } from "vitest";

test("assert.containsAllKeys", () => {
  assert.containsAllKeys({ foo: 1, bar: 2, baz: 3 }, ["foo", "baz"]);
  assert.containsAllKeys({ foo: 1, bar: 2, baz: 3 }, ["foo", "bar", "baz"]);
  assert.containsAllKeys({ foo: 1, bar: 2, baz: 3 }, { foo: 30, baz: 1337 });
  assert.containsAllKeys(
    { foo: 1, bar: 2, baz: 3 },
    { foo: 30, bar: 99, baz: 1337 }
  );
  assert.containsAllKeys(
    new Map([
      [{ foo: 1 }, "bar"],
      ["key", "value"],
    ]),
    [{ foo: 1 }]
  );
  assert.containsAllKeys(
    new Map([
      [{ foo: 1 }, "bar"],
      ["key", "value"],
    ]),
    [{ foo: 1 }, "key"]
  );
  assert.containsAllKeys(new Set([{ foo: "bar" }, "anotherKey"]), [
    { foo: "bar" },
  ]);
  assert.containsAllKeys(new Set([{ foo: "bar" }, "anotherKey"]), [
    { foo: "bar" },
    "anotherKey",
  ]);
});
```

## doesNotHaveAnyKeys

- **타입:**  
  `<T>(object: T, keys: Array<Object | string> | { [key: string]: any }, message?: string) => void`

객체가 **주어진 키 중 아무 것도 가지고 있지 않은지** 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.doesNotHaveAnyKeys", () => {
  assert.doesNotHaveAnyKeys({ foo: 1, bar: 2, baz: 3 }, [
    "one",
    "two",
    "example",
  ]);
  assert.doesNotHaveAnyKeys(
    { foo: 1, bar: 2, baz: 3 },
    { one: 1, two: 2, example: "foo" }
  );
  assert.doesNotHaveAnyKeys(
    new Map([
      [{ foo: 1 }, "bar"],
      ["key", "value"],
    ]),
    [{ one: "two" }, "example"]
  );
  assert.doesNotHaveAnyKeys(new Set([{ foo: "bar" }, "anotherKey"]), [
    { one: "two" },
    "example",
  ]);
});
```

## doesNotHaveAllKeys

- **타입:**  
  `<T>(object: T, keys: Array<Object | string> | { [key: string]: any }, message?: string) => void`

객체가 주어진 키 **모두를 갖고 있지 않은 경우**를 확인합니다. 하나라도 빠지면 통과입니다.

```ts
import { assert, test } from "vitest";

test("assert.doesNotHaveAllKeys", () => {
  assert.doesNotHaveAllKeys({ foo: 1, bar: 2, baz: 3 }, [
    "foo",
    "bar",
    "missing",
  ]);
  assert.doesNotHaveAllKeys({ foo: 1 }, ["foo", "bar"]);
});
```

## hasAnyDeepKeys

- **타입:**  
  `<T>(object: T, keys: Array<Object | string> | { [key: string]: any }, message?: string) => void`

Set 또는 Map에서 **깊은 비교**를 통해 **하나 이상의 키**를 가지고 있는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.hasAnyDeepKeys", () => {
  assert.hasAnyDeepKeys(
    new Map([
      [{ one: "one" }, "valueOne"],
      [1, 2],
    ]),
    { one: "one" }
  );
  assert.hasAnyDeepKeys(
    new Map([
      [{ one: "one" }, "valueOne"],
      [1, 2],
    ]),
    [{ one: "one" }, { two: "two" }]
  );
  assert.hasAnyDeepKeys(
    new Map([
      [{ one: "one" }, "valueOne"],
      [{ two: "two" }, "valueTwo"],
    ]),
    [{ one: "one" }, { two: "two" }]
  );
  assert.hasAnyDeepKeys(new Set([{ one: "one" }, { two: "two" }]), {
    one: "one",
  });
  assert.hasAnyDeepKeys(new Set([{ one: "one" }, { two: "two" }]), [
    { one: "one" },
    { three: "three" },
  ]);
  assert.hasAnyDeepKeys(new Set([{ one: "one" }, { two: "two" }]), [
    { one: "one" },
    { two: "two" },
  ]);
});
```

## hasAllDeepKeys

- **타입:**  
  `<T>(object: T, keys: Array<Object | string> | { [key: string]: any }, message?: string) => void`

Set 또는 Map이 깊은 비교 기준으로 **모든 키**를 가지고 있는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.hasAllDeepKeys", () => {
  assert.hasAllDeepKeys(new Map([[{ one: "one" }, "valueOne"]]), {
    one: "one",
  });
  assert.hasAllDeepKeys(
    new Map([
      [{ one: "one" }, "valueOne"],
      [{ two: "two" }, "valueTwo"],
    ]),
    [{ one: "one" }, { two: "two" }]
  );
  assert.hasAllDeepKeys(new Set([{ one: "one" }]), { one: "one" });
  assert.hasAllDeepKeys(new Set([{ one: "one" }, { two: "two" }]), [
    { one: "one" },
    { two: "two" },
  ]);
});
```

## containsAllDeepKeys

- **타입:**  
  `<T>(object: T, keys: Array<Object | string> | { [key: string]: any }, message?: string) => void`

깊은 비교 기준으로 **주어진 키들을 모두 포함**하는지 확인합니다. 여분의 키가 있어도 됩니다.

```ts
import { assert, test } from "vitest";

test("assert.containsAllDeepKeys", () => {
  assert.containsAllDeepKeys(
    new Map([
      [{ one: "one" }, "valueOne"],
      [1, 2],
    ]),
    { one: "one" }
  );
  assert.containsAllDeepKeys(
    new Map([
      [{ one: "one" }, "valueOne"],
      [{ two: "two" }, "valueTwo"],
    ]),
    [{ one: "one" }, { two: "two" }]
  );
  assert.containsAllDeepKeys(new Set([{ one: "one" }, { two: "two" }]), {
    one: "one",
  });
  assert.containsAllDeepKeys(new Set([{ one: "one" }, { two: "two" }]), [
    { one: "one" },
    { two: "two" },
  ]);
});
```

## doesNotHaveAnyDeepKeys

- **타입:**  
  `<T>(object: T, keys: Array<Object | string> | { [key: string]: any }, message?: string) => void`

깊은 비교 기준으로 **하나도 포함되어 있지 않은지** 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.doesNotHaveAnyDeepKeys", () => {
  assert.doesNotHaveAnyDeepKeys(
    new Map([
      [{ one: "one" }, "valueOne"],
      [1, 2],
    ]),
    { thisDoesNot: "exist" }
  );
  assert.doesNotHaveAnyDeepKeys(
    new Map([
      [{ one: "one" }, "valueOne"],
      [{ two: "two" }, "valueTwo"],
    ]),
    [{ twenty: "twenty" }, { fifty: "fifty" }]
  );
  assert.doesNotHaveAnyDeepKeys(new Set([{ one: "one" }, { two: "two" }]), {
    twenty: "twenty",
  });
  assert.doesNotHaveAnyDeepKeys(new Set([{ one: "one" }, { two: "two" }]), [
    { twenty: "twenty" },
    { fifty: "fifty" },
  ]);
});
```

## doesNotHaveAllDeepKeys

- **타입:**  
  `<T>(object: T, keys: Array<Object | string> | { [key: string]: any }, message?: string) => void`

깊은 비교 기준으로 **모든 키를 포함하고 있지 않은지** 확인합니다. 일부만 포함돼도 통과합니다.

```ts
import { assert, test } from "vitest";

test("assert.doesNotHaveAllDeepKeys", () => {
  assert.doesNotHaveAllDeepKeys(
    new Map([
      [{ one: "one" }, "valueOne"],
      [1, 2],
    ]),
    { thisDoesNot: "exist" }
  );
  assert.doesNotHaveAllDeepKeys(
    new Map([
      [{ one: "one" }, "valueOne"],
      [{ two: "two" }, "valueTwo"],
    ]),
    [{ twenty: "twenty" }, { one: "one" }]
  );
  assert.doesNotHaveAllDeepKeys(new Set([{ one: "one" }, { two: "two" }]), {
    twenty: "twenty",
  });
  assert.doesNotHaveAllDeepKeys(new Set([{ one: "one" }, { two: "two" }]), [
    { one: "one" },
    { fifty: "fifty" },
  ]);
});
```

## throws

- **타입:**
  - `(fn: () => void, errMsgMatcher?: RegExp | string, ignored?: any, message?: string) => void`
  - `(fn: () => void, errorLike?: ErrorConstructor | Error | null, errMsgMatcher?: RegExp | string | null, message?: string) => void`
- **별칭:** `throw`, `Throw`

함수 `fn`이 예외를 던지는지 확인합니다. 예외 타입 또는 메시지 패턴도 함께 확인할 수 있습니다.

```ts
import { assert, test } from "vitest";

function fn() {
  throw new ReferenceError("Error thrown must have this msg");
}

const errorInstance = new ReferenceError("Error thrown must have this msg");

test("assert.throws", () => {
  assert.throws(fn, "Error thrown must have this msg");
  assert.throws(fn, /Error thrown must have a msg that matches this/);
  assert.throws(fn, ReferenceError);
  assert.throws(fn, errorInstance);
  assert.throws(
    fn,
    ReferenceError,
    "Error thrown must be a ReferenceError and have this msg"
  );
  assert.throws(
    fn,
    errorInstance,
    "Error thrown must be the same errorInstance and have this msg"
  );
  assert.throws(
    fn,
    ReferenceError,
    /Error thrown must be a ReferenceError and match this/
  );
  assert.throws(
    fn,
    errorInstance,
    /Error thrown must be the same errorInstance and match this/
  );
});
```

## doesNotThrow

- **타입:** 위와 동일

함수 `fn`이 **예외를 던지지 않는지** 확인합니다.

```ts
import { assert, test } from "vitest";

function fn() {
  return "정상 작동";
}

test("assert.doesNotThrow", () => {
  assert.doesNotThrow(fn, "예외가 발생하면 안 됩니다.");
});
```

## operator

- **타입:** `(val1: OperatorComparable, operator: Operator, val2: OperatorComparable, message?: string) => void`

`val1`과 `val2`를 주어진 비교 연산자로 비교합니다. (`<`, `>`, `===`, `!==` 등)

```ts
import { assert, test } from "vitest";

test("assert.operator", () => {
  assert.operator(1, "<", 2, "1은 2보다 작습니다.");
});
```

## closeTo

- **타입:** `(actual: number, expected: number, delta: number, message?: string) => void`
- **별칭:** `approximately`

숫자 `actual`이 `expected`와 ± `delta` 범위 안에 있는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.closeTo", () => {
  assert.closeTo(1.5, 1, 0.5, "숫자가 허용 오차 내에 있습니다.");
});
```

## sameMembers

- **타입:** `<T>(set1: T[], set2: T[], message?: string) => void`

두 배열이 **순서와 상관없이** 같은 값을 모두 포함하고 있는지 (=== 비교) 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.sameMembers", () => {
  assert.sameMembers([1, 2, 3], [2, 1, 3], "같은 구성원입니다.");
});
```

## notSameMembers

- **타입:** 동일

두 배열이 같은 구성원이 **아닌지** 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notSameMembers", () => {
  assert.notSameMembers([1, 2, 3], [5, 1, 3], "다른 구성원입니다.");
});
```

## sameDeepMembers

- **타입:** `<T>(set1: T[], set2: T[], message?: string) => void`

두 배열이 **깊은 비교**로 같은 값을 가지고 있는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.sameDeepMembers", () => {
  assert.sameDeepMembers(
    [{ a: 1 }, { b: 2 }, { c: 3 }],
    [{ b: 2 }, { a: 1 }, { c: 3 }],
    "깊은 동일 구성원입니다."
  );
});
```

## notSameDeepMembers

- **타입:** 동일

두 배열이 깊은 비교 기준으로 **같지 않은 구성원**을 가졌는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notSameDeepMembers", () => {
  assert.notSameDeepMembers(
    [{ a: 1 }, { b: 2 }, { c: 3 }],
    [{ b: 2 }, { a: 1 }, { z: 5 }],
    "구성원이 다릅니다."
  );
});
```

## sameOrderedMembers

- **타입:** `<T>(set1: T[], set2: T[], message?: string) => void`

두 배열이 **같은 순서로** 같은 멤버를 가지고 있는지 (=== 비교) 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.sameOrderedMembers", () => {
  assert.sameOrderedMembers(
    [1, 2, 3],
    [1, 2, 3],
    "순서까지 같은 구성원입니다."
  );
});
```

## notSameOrderedMembers

- **타입:** 동일

두 배열이 **순서가 다르거나 다른 값**을 가진 경우를 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notSameOrderedMembers", () => {
  assert.notSameOrderedMembers([1, 2, 3], [2, 1, 3], "순서가 다릅니다.");
});
```

## sameDeepOrderedMembers

- **타입:** `<T>(set1: T[], set2: T[], message?: string) => void`

두 배열이 **같은 순서로 깊은 동등성**을 가진 구성원인지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.sameDeepOrderedMembers", () => {
  assert.sameDeepOrderedMembers(
    [{ a: 1 }, { b: 2 }, { c: 3 }],
    [{ a: 1 }, { b: 2 }, { c: 3 }],
    "순서까지 같은 깊은 구성원입니다."
  );
});
```

## notSameDeepOrderedMembers

- **타입:** 동일

두 배열이 순서가 다르거나 깊은 동등성이 일치하지 않는 경우를 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notSameDeepOrderedMembers", () => {
  assert.notSameDeepOrderedMembers(
    [{ a: 1 }, { b: 2 }, { c: 3 }],
    [{ a: 1 }, { b: 2 }, { z: 5 }]
  );
  assert.notSameDeepOrderedMembers(
    [{ a: 1 }, { b: 2 }, { c: 3 }],
    [{ b: 2 }, { a: 1 }, { c: 3 }]
  );
});
```

## includeMembers

- **타입:** `<T>(superset: T[], subset: T[], message?: string) => void`

`subset`이 `superset`에 **순서 없이 포함되어 있는지** 확인합니다. 중복은 무시됩니다.

```ts
import { assert, test } from "vitest";

test("assert.includeMembers", () => {
  assert.includeMembers([1, 2, 3], [2, 1, 2], "중복은 무시하고 포함됨을 확인");
});
```

## notIncludeMembers

- **타입:** 동일

`subset`이 `superset`에 포함되어 있지 않음을 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notIncludeMembers", () => {
  assert.notIncludeMembers([1, 2, 3], [5, 1], "5는 포함되지 않음");
});
```

## includeDeepMembers

- **타입:** `<T>(superset: T[], subset: T[], message?: string) => void`

`subset`이 `superset`에 **깊은 동등성 기준으로 포함**되어 있는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.includeDeepMembers", () => {
  assert.includeDeepMembers(
    [{ a: 1 }, { b: 2 }, { c: 3 }],
    [{ b: 2 }, { a: 1 }, { b: 2 }]
  );
});
```

## notIncludeDeepMembers

- **타입:** 동일

`subset`이 `superset`에 **깊은 동등성 기준으로 포함되지 않았는지** 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notIncludeDeepMembers", () => {
  assert.notIncludeDeepMembers(
    [{ a: 1 }, { b: 2 }, { c: 3 }],
    [{ b: 2 }, { f: 5 }]
  );
});
```

## includeOrderedMembers

- **타입:** `<T>(superset: T[], subset: T[], message?: string) => void`

`subset`이 `superset`의 앞에서부터 **같은 순서로 포함**되는지 확인합니다. `===` 비교 사용.

```ts
import { assert, test } from "vitest";

test("assert.includeOrderedMembers", () => {
  assert.includeOrderedMembers([1, 2, 3], [1, 2]);
});
```

## notIncludeOrderedMembers

- **타입:** 동일

`subset`이 `superset`에 **순서대로 포함되지 않았는지** 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notIncludeOrderedMembers", () => {
  assert.notIncludeOrderedMembers([1, 2, 3], [2, 1]);
  assert.notIncludeOrderedMembers([1, 2, 3], [2, 3]);
});
```

## includeDeepOrderedMembers

- **타입:** `<T>(superset: T[], subset: T[], message?: string) => void`

`subset`이 `superset`에 **깊은 동등성 기준으로 순서대로 포함**되어 있는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.includeDeepOrderedMembers", () => {
  assert.includeDeepOrderedMembers(
    [{ a: 1 }, { b: 2 }, { c: 3 }],
    [{ a: 1 }, { b: 2 }]
  );
});
```

## notIncludeDeepOrderedMembers

- **타입:** 동일

`subset`이 `superset`에 **깊은 동등성 기준으로 순서대로 포함되지 않았는지** 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.notIncludeDeepOrderedMembers", () => {
  assert.notIncludeDeepOrderedMembers(
    [{ a: 1 }, { b: 2 }, { c: 3 }],
    [{ a: 1 }, { f: 5 }]
  );
  assert.notIncludeDeepOrderedMembers(
    [{ a: 1 }, { b: 2 }, { c: 3 }],
    [{ b: 2 }, { a: 1 }]
  );
  assert.notIncludeDeepOrderedMembers(
    [{ a: 1 }, { b: 2 }, { c: 3 }],
    [{ b: 2 }, { c: 3 }]
  );
});
```

## oneOf

- **타입:** `<T>(inList: T, list: T[], message?: string) => void`

`inList` 값이 `list`에 포함되어 있는지 확인합니다. 오직 **flat 배열**만 사용 가능하며, 값은 **객체가 아니어야 합니다.**

```ts
import { assert, test } from "vitest";

test("assert.oneOf", () => {
  assert.oneOf(1, [2, 1], "1은 목록에 포함되어 있습니다.");
});
```

## changes

- **타입:** `<T>(modifier: Function, object: T, property: string, message?: string) => void`

`modifier` 함수가 실행된 후 객체의 속성이 변경되었는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.changes", () => {
  const obj = { val: 10 };
  function fn() {
    obj.val = 22;
  }
  assert.changes(fn, obj, "val");
});
```

## changesBy

- **타입:** `<T>(modifier: Function, object: T, property: string, change: number, message?: string) => void`

속성의 값이 `modifier` 실행 후 주어진 `change`만큼 **정확히** 변화했는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.changesBy", () => {
  const obj = { val: 10 };
  function fn() {
    obj.val += 2;
  }
  assert.changesBy(fn, obj, "val", 2);
});
```

## doesNotChange

- **타입:** `<T>(modifier: Function, object: T, property: string, message?: string) => void`

`modifier` 함수 실행 전후로 속성 값이 **변하지 않았는지** 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.doesNotChange", () => {
  const obj = { val: 10 };
  function fn() {
    const unused = 99;
  }
  assert.doesNotChange(fn, obj, "val");
});
```

## changesButNotBy

- **타입:**  
  `<T>(modifier: Function, object: T, property: string, change: number, message?: string) => void`

`modifier` 실행 후, 해당 속성 값이 **변하긴 했지만 주어진 change 값과는 다른** 만큼 변경되었는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.changesButNotBy", () => {
  const obj = { val: 10 };
  function fn() {
    obj.val += 3;
  }
  assert.changesButNotBy(fn, obj, "val", 2);
});
```

## increases

- **타입:**  
  `<T>(modifier: Function, object: T, property: string, message?: string) => void`

속성 값이 증가했는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.increases", () => {
  const obj = { val: 1 };
  function fn() {
    obj.val += 1;
  }
  assert.increases(fn, obj, "val");
});
```

## increasesBy

- **타입:**  
  `<T>(modifier: Function, object: T, property: string, increase: number, message?: string) => void`

속성 값이 지정한 값만큼 **정확히 증가**했는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.increasesBy", () => {
  const obj = { val: 1 };
  function fn() {
    obj.val += 5;
  }
  assert.increasesBy(fn, obj, "val", 5);
});
```

## doesNotIncrease

- **타입:**  
  `<T>(modifier: Function, object: T, property: string, message?: string) => void`

속성 값이 **증가하지 않았는지** 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.doesNotIncrease", () => {
  const obj = { val: 1 };
  function fn() {
    obj.val = 1;
  }
  assert.doesNotIncrease(fn, obj, "val");
});
```

## increasesButNotBy

- **타입:**  
  `(fn: () => any, getter: () => number, delta: number, message?: string) => void`

- **설명:**  
  `getter()` 값이 증가하지만, **정확히 delta만큼 증가하지는 않을 경우** 통과합니다.

- **예시:**

```ts
import { assert, test } from "vitest";

test("increasesButNotBy 예시", () => {
  let balance = 100;
  const deposit = () => {
    balance += 50;
  };

  assert.increasesButNotBy(deposit, () => balance, 30);
});
```

## decreases

- **타입:**  
  `<T>(modifier: Function, object: T, property: string, message?: string) => void`

속성 값이 감소했는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.decreases", () => {
  const obj = { val: 5 };
  function fn() {
    obj.val -= 1;
  }
  assert.decreases(fn, obj, "val");
});
```

## decreasesBy

- **타입:**  
  `<T>(modifier: Function, object: T, property: string, decrease: number, message?: string) => void`

속성 값이 정확히 주어진 만큼 감소했는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.decreasesBy", () => {
  const obj = { val: 5 };
  function fn() {
    obj.val -= 2;
  }
  assert.decreasesBy(fn, obj, "val", 2);
});
```

## doesNotDecrease

- **타입:**  
  `<T>(modifier: Function, object: T, property: string, message?: string) => void`

속성 값이 감소하지 않았는지 확인합니다.

```ts
import { assert, test } from "vitest";

test("assert.doesNotDecrease", () => {
  const obj = { val: 5 };
  function fn() {
    obj.val += 1;
  }
  assert.doesNotDecrease(fn, obj, "val");
});
```

## doesNotDecreaseBy

- **타입:**  
  `(fn: () => any, getter: () => number, delta: number, message?: string) => void`

- **설명:**  
  `getter()` 값이 delta만큼 **감소하지 않을 경우** 통과합니다.

- **예시:**

```ts
import { assert, test } from "vitest";

test("doesNotDecreaseBy 예시", () => {
  let temp = 30;
  const coolDown = () => {
    temp -= 5;
  };

  assert.doesNotDecreaseBy(coolDown, () => temp, 10);
});
```

## decreasesButNotBy

- **타입:**  
  `(fn: () => any, getter: () => number, delta: number, message?: string) => void`

- **설명:**  
  `getter()` 값이 감소는 하지만, **delta만큼 정확히 감소하지 않을 경우** 통과합니다.

- **예시:**

```ts
import { assert, test } from "vitest";

test("decreasesButNotBy 예시", () => {
  let volume = 80;
  const reduce = () => {
    volume -= 15;
  };

  assert.decreasesButNotBy(reduce, () => volume, 10);
});
```

## ifError

- **타입:**  
  `(value: any, error?: Error, message?: string) => void`

- **설명:**  
  `value`가 falsy하거나 error 객체일 경우 **예외가 발생해야** 통과합니다.

- **예시:**

```ts
import { assert, test } from "vitest";

test("ifError 예시", () => {
  assert.ifError(null); // null은 falsy → 통과
  assert.ifError(undefined); // undefined도 falsy → 통과
});
```

## isExtensible

- **타입:**  
  `(obj: object, message?: string) => void`

- **설명:**  
  객체가 **확장 가능(extensible)** 상태일 경우 통과합니다.  
  `Object.isExtensible()`이 `true`를 반환해야 합니다.

- **예시:**

```ts
import { assert, test } from "vitest";

test("isExtensible 예시", () => {
  const obj = {};
  assert.isExtensible(obj);
});
```

## isNotExtensible

- **타입:**  
  `(obj: object, message?: string) => void`

- **설명:**  
  객체가 **확장 불가능(non-extensible)** 상태일 경우 통과합니다.

- **예시:**

```ts
import { assert, test } from "vitest";

test("isNotExtensible 예시", () => {
  const obj = Object.preventExtensions({});
  assert.isNotExtensible(obj);
});
```

## isSealed

- **타입:**  
  `(obj: object, message?: string) => void`

- **설명:**  
  객체가 **봉인(sealed)** 상태일 경우 통과합니다.  
  프로퍼티 추가/삭제가 불가능하며, 모든 프로퍼티가 configurable: false여야 합니다.

- **예시:**

```ts
import { assert, test } from "vitest";

test("isSealed 예시", () => {
  const obj = Object.seal({ a: 1 });
  assert.isSealed(obj);
});
```

## isNotSealed

- **타입:**  
  `(obj: object, message?: string) => void`

- **설명:**  
  객체가 **봉인되지 않았을 경우** 통과합니다.

- **예시:**

```ts
import { assert, test } from "vitest";

test("isNotSealed 예시", () => {
  const obj = {};
  assert.isNotSealed(obj);
});
```

## isFrozen

- **타입:**  
  `(obj: object, message?: string) => void`

- **설명:**  
  객체가 **동결(frozen)** 상태일 경우 통과합니다.  
  프로퍼티 수정, 추가, 삭제 모두 불가능해야 합니다.

- **예시:**

```ts
import { assert, test } from "vitest";

test("isFrozen 예시", () => {
  const obj = Object.freeze({ a: 1 });
  assert.isFrozen(obj);
});
```

## isNotFrozen

- **타입:**  
  `(obj: object, message?: string) => void`

- **설명:**  
  객체가 **동결되지 않았을 경우** 통과합니다.

- **예시:**

```ts
import { assert, test } from "vitest";

test("isNotFrozen 예시", () => {
  const obj = { b: 2 };
  assert.isNotFrozen(obj);
});
```

## isEmpty

- **타입:**  
  `(value: any, message?: string) => void`

- **설명:**  
  배열, 객체, 문자열 등에서 **내용이 없을 경우** 통과합니다.

  - 빈 배열: `[]`
  - 빈 객체: `{}`
  - 빈 문자열: `''`
  - 빈 Set/Map도 포함됩니다.

- **예시:**

```ts
import { assert, test } from "vitest";

test("isEmpty 예시", () => {
  assert.isEmpty([]);
  assert.isEmpty("");
  assert.isEmpty({});
});
```

## isNotEmpty

- **타입:**  
  `(value: any, message?: string) => void`

- **설명:**  
  배열, 객체, 문자열 등에 **무언가 내용이 있을 경우** 통과합니다.

- **예시:**

```ts
import { assert, test } from "vitest";

test("isNotEmpty 예시", () => {
  assert.isNotEmpty([1]);
  assert.isNotEmpty("abc");
  assert.isNotEmpty({ a: 1 });
});
```
