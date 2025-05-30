# Mock Functions (모의 함수)

Vitest에서는 `vi.fn`을 사용하여 모의 함수를 생성하고, 호출 여부나 호출 인자를 추적할 수 있습니다.  
이미 존재하는 객체의 메서드를 감시하려면 `vi.spyOn`을 사용합니다.

```ts
import { vi } from "vitest";

const fn = vi.fn();
fn("hello world");
fn.mock.calls[0] === ["hello world"];

const market = {
  getApples: () => 100,
};

const getApplesSpy = vi.spyOn(market, "getApples");
market.getApples();
getApplesSpy.mock.calls.length === 1;
```

모의 함수의 결과를 검증할 때는 [`expect`](/api/expect)를 사용하며, [`toHaveBeenCalled`](/api/expect#tohavebeencalled) 등의 mock 전용 assertion을 활용할 수 있습니다.

> 💡 **참고:** 아래의 타입 정의에서 `<T>`는 사용자가 정의한 함수의 타입을 의미합니다.

## getMockImplementation

```ts
function getMockImplementation(): T | undefined;
```

현재 모의 함수의 구현체를 반환합니다.

- `vi.fn()`으로 생성된 경우 등록된 구현을 반환합니다.
- `vi.spyOn()`으로 생성된 경우, 별도로 구현을 등록하지 않으면 `undefined`를 반환합니다.

## getMockName

```ts
function getMockName(): string;
```

`.mockName(name)`으로 지정한 이름을 반환합니다.  
기본값은 `'vi.fn()'`입니다.

## mockClear

```ts
function mockClear(): MockInstance<T>;
```

모든 호출 기록을 초기화합니다.

- `.mock.calls`, `.mock.results` 등의 정보를 초기 상태로 되돌립니다.
- 구현 함수(`mockImplementation`)는 유지됩니다.

```ts
const person = {
  greet: (name: string) => `Hello ${name}`,
};
const spy = vi.spyOn(person, "greet").mockImplementation(() => "mocked");
expect(person.greet("Alice")).toBe("mocked");
expect(spy.mock.calls).toEqual([["Alice"]]);

// 호출 기록 초기화 (구현 유지)
spy.mockClear();
expect(spy.mock.calls).toEqual([]);
expect(person.greet("Bob")).toBe("mocked");
```

> 자동화 Tip: 설정 파일에서 [`clearMocks`](/config/#clearmocks)를 활성화하면 각 테스트 전 자동으로 `mockClear()`가 호출됩니다.

## mockName

```ts
function mockName(name: string): MockInstance<T>;
```

모의 함수의 이름을 지정합니다.  
Assertion 실패 시 어떤 mock이 실패했는지 구분하는 데 유용합니다.

## mockImplementation

```ts
function mockImplementation(fn: T): MockInstance<T>;
```

모의 함수에 실제 구현을 등록합니다.  
TypeScript는 원래 함수와 동일한 인자 및 반환 타입을 기대합니다.

```ts
const mockFn = vi.fn().mockImplementation((apples: number) => apples + 1);

mockFn(0); // 1
mockFn(1); // 2

mockFn.mock.calls[0][0] === 0;
mockFn.mock.calls[1][0] === 1;
```

## ⏱ mockImplementationOnce

```ts
function mockImplementationOnce(fn: T): MockInstance<T>;
```

**한 번만** 사용할 임시 구현을 등록합니다.  
여러 번 체이닝하여 호출 횟수에 따라 결과를 다르게 만들 수 있습니다.

```ts
const myMockFn = vi
  .fn()
  .mockImplementationOnce(() => true)
  .mockImplementationOnce(() => false);

myMockFn(); // true
myMockFn(); // false
```

이후에는 기본 구현 또는 `.mockImplementation()`에서 등록된 함수가 사용됩니다.

```ts
const myMockFn = vi
  .fn(() => "default")
  .mockImplementationOnce(() => "first call")
  .mockImplementationOnce(() => "second call");

console.log(myMockFn(), myMockFn(), myMockFn(), myMockFn());
// 'first call', 'second call', 'default', 'default'
```

## withImplementation

```ts
function withImplementation(fn: T, cb: () => void): MockInstance<T>;
function withImplementation(
  fn: T,
  cb: () => Promise<void>
): Promise<MockInstance<T>>;
```

콜백이 실행되는 동안 임시 구현으로 대체합니다.

```ts
const myMockFn = vi.fn(() => "original");

myMockFn.withImplementation(
  () => "temp",
  () => {
    myMockFn(); // 'temp'
  }
);

myMockFn(); // 'original'
```

비동기 콜백도 지원하며, 반드시 `await` 해야 정상 복원됩니다.

```ts
await myMockFn.withImplementation(
  () => "temp",
  async () => {
    myMockFn(); // 'temp'
  }
);

myMockFn(); // 'original'
```

> ⚠️ `.mockImplementationOnce()`보다 우선 적용됩니다.

## mockResolvedValue

```ts
function mockResolvedValue(value: Awaited<ReturnType<T>>): MockInstance<T>;
```

비동기 함수가 호출될 때 지정된 값을 resolve합니다.

```ts
const asyncMock = vi.fn().mockResolvedValue(42);
await asyncMock(); // 42
```

## mockResolvedValueOnce

```ts
function mockResolvedValueOnce(value: Awaited<ReturnType<T>>): MockInstance<T>;
```

**다음 한 번의 호출만** 지정된 값을 resolve합니다. 이후 호출은 기본 값 또는 `.mockResolvedValue()`에 따라 처리됩니다.

```ts
const asyncMock = vi
  .fn()
  .mockResolvedValue("default")
  .mockResolvedValueOnce("first call")
  .mockResolvedValueOnce("second call");

await asyncMock(); // 'first call'
await asyncMock(); // 'second call'
await asyncMock(); // 'default'
```

## ❌ mockRejectedValue

```ts
function mockRejectedValue(value: unknown): MockInstance<T>;
```

비동기 함수가 호출될 때 지정된 오류를 reject합니다.

```ts
const asyncMock = vi.fn().mockRejectedValue(new Error("Async error"));
await asyncMock(); // throws Error('Async error')
```

## ❌ mockRejectedValueOnce

```ts
function mockRejectedValueOnce(value: unknown): MockInstance<T>;
```

**다음 한 번의 호출만** 지정된 오류로 reject합니다.

```ts
const asyncMock = vi
  .fn()
  .mockResolvedValueOnce("first call")
  .mockRejectedValueOnce(new Error("Async error"));

await asyncMock(); // 'first call'
await asyncMock(); // throws Error('Async error')
```

## 🔄 mockReset

```ts
function mockReset(): MockInstance<T>;
```

- `mockClear()`와 동일하게 모든 호출 기록을 지움
- 등록된 구현도 **초기 상태로 리셋**
- `.mockImplementationOnce()`로 등록된 임시 구현도 모두 제거됨

```ts
const spy = vi.spyOn(obj, "method").mockImplementation(() => "mocked");
spy.mockReset();

// obj.method는 여전히 spy지만 구현은 원래대로 복원됨
```

> 자동화 Tip: 설정 파일에서 [`mockReset`](/config/#mockreset) 옵션을 사용하면 테스트마다 자동으로 리셋됩니다.

## mockRestore

```ts
function mockRestore(): MockInstance<T>;
```

- `mockReset()`의 기능 포함
- - **원래의 객체 메서드로 복원**  
    즉, spy 자체가 제거되고 원본 메서드만 남습니다.

```ts
const person = {
  greet: (name: string) => `Hello ${name}`,
};

const spy = vi.spyOn(person, "greet").mockImplementation(() => "mocked");
expect(person.greet("Alice")).toBe("mocked");

spy.mockRestore();
expect(person.greet("Bob")).toBe("Hello Bob");
```

> 설정 파일에서 [`restoreMocks`](/config/#restoremocks)를 활성화하면 모든 spy가 테스트 전에 자동 복원됩니다.

## mockReturnValue

```ts
function mockReturnValue(value: ReturnType<T>): MockInstance<T>;
```

매 호출마다 지정된 값을 반환합니다.

```ts
const mock = vi.fn();
mock.mockReturnValue(42);
mock(); // 42

mock.mockReturnValue(100);
mock(); // 100
```

## mockReturnValueOnce

```ts
function mockReturnValueOnce(value: ReturnType<T>): MockInstance<T>;
```

다음 한 번의 호출에만 지정된 값을 반환합니다.

```ts
const mock = vi
  .fn()
  .mockReturnValue("default")
  .mockReturnValueOnce("first call")
  .mockReturnValueOnce("second call");

console.log(mock(), mock(), mock(), mock());
// 'first call', 'second call', 'default', 'default'
```

## mockReturnThis

```ts
function mockReturnThis(): MockInstance<T>;
```

함수 호출 시 `this`를 그대로 반환합니다.

```ts
spy.mockImplementation(function () {
  return this;
});
```

위 코드를 간단히 쓸 수 있도록 하는 단축 메서드입니다.

## mock 객체 속성

모든 `vi.fn()` 또는 `vi.spyOn()`으로 생성된 모의 함수에는 `.mock` 속성이 있으며, 다음과 같은 다양한 추적 정보가 포함되어 있습니다.

## mock.calls

```ts
const calls: Parameters<T>[];
```

함수가 호출된 모든 인자들의 배열입니다.  
각 항목은 개별 호출 시의 인자 배열입니다.

```ts
const fn = vi.fn();
fn("a", "b");
fn("c");

fn.mock.calls ===
  [
    ["a", "b"], // 첫 번째 호출
    ["c"], // 두 번째 호출
  ];
```

## mock.lastCall

```ts
const lastCall: Parameters<T> | undefined;
```

마지막 호출의 인자 배열입니다.  
호출된 적이 없다면 `undefined`를 반환합니다.

## mock.results

```ts
type MockResult<T> =
  | { type: "return"; value: T }
  | { type: "throw"; value: any }
  | { type: "incomplete"; value: undefined };

const results: MockResult<ReturnType<T>>[];
```

각 호출에서 반환된 값 또는 발생한 예외를 추적합니다.

```ts
const fn = vi.fn()
  .mockReturnValueOnce('result')
  .mockImplementationOnce(() => { throw new Error('fail') })

fn() // 반환값: 'result'
try {
  fn() // 예외 발생
} catch {}

fn.mock.results === [
  { type: 'return', value: 'result' },
  { type: 'throw', value: Error(...) },
]
```

## mock.settledResults

```ts
type MockSettledResult<T> =
  | { type: "fulfilled"; value: T }
  | { type: "rejected"; value: any };

const settledResults: MockSettledResult<Awaited<ReturnType<T>>>[];
```

비동기 함수가 resolve 또는 reject된 결과를 포함합니다.

```ts
const fn = vi.fn().mockResolvedValueOnce("done");

const result = fn(); // Promise
fn.mock.settledResults === []; // 아직 비워져 있음

await result;

fn.mock.settledResults === [{ type: "fulfilled", value: "done" }];
```

## mock.invocationCallOrder

```ts
const invocationCallOrder: number[];
```

해당 모의 함수가 호출된 순서를 추적합니다.  
이 값은 **모든 mock 함수들 간에 공유되는 순서**입니다.

```ts
const fn1 = vi.fn();
const fn2 = vi.fn();

fn1();
fn2();
fn1();

fn1.mock.invocationCallOrder === [1, 3];
fn2.mock.invocationCallOrder === [2];
```

## mock.contexts

```ts
const contexts: ThisParameterType<T>[];
```

각 호출에서의 `this` 값을 추적합니다.

```ts
const fn = vi.fn();
const ctx = {};

fn.call(ctx);
fn.apply(ctx);

fn.mock.contexts[0] === ctx;
fn.mock.contexts[1] === ctx;
```

## mock.instances

```ts
const instances: ReturnType<T>[];
```

`new` 키워드로 호출된 경우, 생성된 인스턴스가 여기에 저장됩니다.

```ts
const MyClass = vi.fn();
const a = new MyClass();

MyClass.mock.instances[0] === a;
```

단, 생성자 내부에서 다른 값을 명시적으로 반환했다면 해당 값은 `.results`에 저장되고, `.instances`에는 포함되지 않습니다.

```ts
const Spy = vi.fn(() => ({ name: "mock" }));
const a = new Spy();

Spy.mock.results[0].value === a;
Spy.mock.instances[0] !== a;
```
