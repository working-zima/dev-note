# Test API Reference (테스트 API 레퍼런스)

## 타입 정의

```ts
type Awaitable<T> = T | PromiseLike<T>;
type TestFunction = () => Awaitable<void>;

interface TestOptions {
  /**
   * 테스트가 너무 오래 걸리면 실패 처리합니다.
   */
  timeout?: number;
  /**
   * 실패 시 테스트를 재시도할 횟수입니다.
   *
   * @default 0
   */
  retry?: number;
  /**
   * 실패 여부와 관계없이 테스트를 반복 실행합니다.
   * `retry`와 함께 사용할 경우 각 반복마다 재시도합니다.
   * 랜덤하게 실패하는 테스트를 디버깅할 때 유용합니다.
   *
   * @default 0
   */
  repeats?: number;
}
```

`Promise`를 반환하는 테스트 함수는 해당 `Promise`가 해결될 때까지 기다린 뒤 평가합니다. 만약 `Promise`가 거부되면 해당 테스트는 실패 처리됩니다.

> 💡 **참고 (Jest):**  
> Jest에서는 `TestFunction`이 `(done: DoneCallback) => void` 형식도 가능합니다. 이 경우 `done()`을 호출해야 테스트가 종료됩니다.  
> Vitest에서는 `async` 함수를 사용하여 동일한 효과를 낼 수 있으며, [마이그레이션 가이드 - Done Callback 섹션](/guide/migration#done-callback)을 참고하세요.

## 테스트 옵션 설정 방식

테스트의 옵션은 두 가지 방식으로 설정할 수 있으며, 동작은 동일합니다.

### 1. 체이닝 방식

```ts
import { test } from "vitest";

test.skip("현재 실패하는 테스트", () => {
  // 생략된 실패 코드
});

test.concurrent.skip("현재 실패하는 병렬 테스트", () => {
  // 생략된 실패 코드
});
```

### 2. 옵션 객체 전달 방식

```ts
import { test } from "vitest";

test("현재 실패하는 테스트", { skip: true }, () => {
  // 생략된 실패 코드
});

test("병렬로 실행될 실패 테스트", { skip: true, concurrent: true }, () => {
  // 생략된 실패 코드
});
```

두 방식 모두 동일하게 동작하며, 스타일의 차이일 뿐입니다.

## ⚠️ timeout 인자와 옵션 객체의 충돌

`timeout`을 마지막 인자로 전달하는 경우 **옵션 객체는 사용할 수 없습니다**:

```ts
// ✅ 작동
test.skip("무거운 테스트", () => {
  // ...
}, 10000);

// ❌ 작동하지 않음
test(
  "무거운 테스트",
  { skip: true },
  () => {
    // ...
  },
  10000
);
```

하지만 `timeout`을 옵션 객체 내부에 포함하면 정상 동작합니다:

```ts
// ✅ 작동
test("무거운 테스트", { skip: true, timeout: 10000 }, () => {
  // ...
});
```

## test 함수

- **별칭(Alias):** `it`

`test` 함수는 하나의 테스트 케이스를 정의하는 기본 함수입니다. 첫 번째 인자로 테스트 이름, 두 번째 인자로 테스트 함수를 받습니다.

선택적으로 세 번째 인자로 타임아웃(밀리초)을 지정할 수 있으며, 기본값은 5초입니다. 전역 설정은 [`testTimeout`](/config/#testtimeout)을 통해 가능합니다.

```ts
import { expect, test } from "vitest";

test("제곱근이 올바르게 계산된다", () => {
  expect(Math.sqrt(4)).toBe(2);
});
```

### test.extend

- **별칭(Alias):** `it.extend`

`test.extend`는 테스트 컨텍스트를 사용자 정의 fixture로 확장하는 데 사용됩니다. 이 메서드는 새로운 `test` 객체를 반환하며, 이를 다시 `extend`하여 계층적으로 구성할 수 있습니다.

```ts
import { expect, test } from "vitest";

const todos = [];
const archive = [];

const myTest = test.extend({
  todos: async ({ task }, use) => {
    todos.push(1, 2, 3);
    await use(todos);
    todos.length = 0;
  },
  archive,
});

myTest("할 일 추가", ({ todos }) => {
  expect(todos.length).toBe(3);
  todos.push(4);
  expect(todos.length).toBe(4);
});
```

### test.skip

- **별칭(Alias):** `it.skip`

일시적으로 테스트 실행을 건너뛰고 싶을 때 사용합니다.

```ts
import { assert, test } from "vitest";

test.skip("이 테스트는 건너뜁니다", () => {
  // 실행되지 않음
  assert.equal(Math.sqrt(4), 3);
});
```

또는 `context.skip()`을 통해 동적으로 스킵할 수도 있습니다:

```ts
test("조건부 스킵", (context) => {
  context.skip();
  assert.equal(Math.sqrt(4), 3);
});
```

Vitest 3.1부터는 조건부로도 가능하며, 두 번째 인자로 메시지도 작성할 수 있습니다:

```ts
test("랜덤 스킵", (context) => {
  context.skip(Math.random() < 0.5, "랜덤하게 건너뜁니다");
});
```

### test.skipIf

- **별칭(Alias):** `it.skipIf`

환경 변수 등에 따라 특정 테스트만 건너뛰고 싶을 때 사용합니다.

```ts
const isDev = process.env.NODE_ENV === "development";

test.skipIf(isDev)("프로덕션 전용 테스트", () => {
  // 프로덕션에서만 실행됨
});
```

### test.runIf

- **별칭(Alias):** `it.runIf`

`test.skipIf`의 반대입니다. 조건이 true일 때만 테스트를 실행합니다.

```ts
const isDev = process.env.NODE_ENV === "development";

test.runIf(isDev)("개발 환경 전용 테스트", () => {
  // 개발 환경에서만 실행됨
});
```

### test.only

- **별칭(Alias):** `it.only`

디버깅을 위해 특정 테스트만 실행하고 싶을 때 사용합니다.

```ts
test.only("이 테스트만 실행됨", () => {
  expect(Math.sqrt(4)).toBe(2);
});
```

> 🛠 전체 스위트 중 특정 파일만 실행하려면 다음과 같이 실행합니다:
>
> ```bash
> vitest interesting.test.ts
> ```

### test.concurrent

- **별칭(Alias):** `it.concurrent`

여러 테스트를 병렬로 실행할 수 있게 합니다. 주로 async 테스트에서 유용합니다.

```ts
import { describe, test } from "vitest";

describe("테스트 스위트", () => {
  test("직렬 테스트", async () => {
    /* ... */
  });
  test.concurrent("병렬 테스트 1", async () => {
    /* ... */
  });
  test.concurrent("병렬 테스트 2", async () => {
    /* ... */
  });
});
```

다음과 같은 조합도 모두 가능합니다:

```ts
test.concurrent.skip(/* ... */);
test.skip.concurrent(/* ... */);
test.only.concurrent(/* ... */);
test.concurrent.todo(/* ... */);
```

병렬 테스트에서는 `expect`를 **테스트 컨텍스트로부터 destructure** 하여 사용해야 snapshot 및 assertion이 올바르게 인식됩니다.

```ts
test.concurrent("병렬 스냅샷", async ({ expect }) => {
  expect(foo).toMatchSnapshot();
});
```

### test.sequential

- **별칭(Alias):** `it.sequential`

`describe.concurrent` 내부에서 일부 테스트만 순차적으로 실행되게 할 수 있습니다.

```ts
test.sequential("순차 테스트 1", async () => {
  /* ... */
});
test.sequential("순차 테스트 2", async () => {
  /* ... */
});
```

### test.todo

- **별칭(Alias):** `it.todo`

나중에 구현할 테스트를 미리 표시하고 싶을 때 사용합니다.

```ts
test.todo("아직 구현되지 않은 테스트");
```

보고서에 할 일 목록처럼 표시됩니다.

### ❌ test.fails

- **별칭(Alias):** `it.fails`

실패를 의도한 테스트입니다. 실패하는 것이 기대되는 경우 사용합니다.

```ts
test.fails("실패가 예상되는 테스트", async () => {
  await expect(myAsyncFunc()).rejects.toBe(1);
});
```

### test.each

- **별칭(Alias):** `it.each`

`test.each`는 동일한 테스트를 다양한 데이터로 반복 실행할 수 있도록 해줍니다.  
Jest와 호환되며, `printf` 스타일 포맷을 활용할 수 있습니다.

#### 배열 기반 사용 예시

```ts
import { expect, test } from "vitest";

test.each([
  [1, 1, 2],
  [1, 2, 3],
  [2, 1, 3],
])("add(%i, %i) -> %i", (a, b, expected) => {
  expect(a + b).toBe(expected);
});
```

출력 결과:

```bash
✓ add(1, 1) -> 2
✓ add(1, 2) -> 3
✓ add(2, 1) -> 3
```

#### 객체 기반 사용 예시 (`$변수`)

```ts
test.each([
  { a: 1, b: 1, expected: 2 },
  { a: 1, b: 2, expected: 3 },
  { a: 2, b: 1, expected: 3 },
])("add($a, $b) -> $expected", ({ a, b, expected }) => {
  expect(a + b).toBe(expected);
});
```

또는 배열 인덱스로도 접근할 수 있습니다:

```ts
test.each([
  [1, 1, 2],
  [1, 2, 3],
  [2, 1, 3],
])("add($0, $1) -> $2", (a, b, expected) => {
  expect(a + b).toBe(expected);
});
```

#### 템플릿 문자열 테이블 방식

```ts
test.each`
  a             | b      | expected
  ${{ val: 1 }} | ${"b"} | ${"1b"}
  ${{ val: 2 }} | ${"b"} | ${"2b"}
  ${{ val: 3 }} | ${"b"} | ${"3b"}
`("add($a.val, $b) -> $expected", ({ a, b, expected }) => {
  expect(a.val + b).toBe(expected);
});
```

Vitest 0.25.3 이상에서는 템플릿 리터럴을 사용한 표 형식도 지원합니다.

```ts
import { expect, test } from "vitest";

test.each`
  a             | b      | expected
  ${1}          | ${1}   | ${2}
  ${"a"}        | ${"b"} | ${"ab"}
  ${[]}         | ${"b"} | ${"b"}
  ${{}}         | ${"b"} | ${"[object Object]b"}
  ${{ asd: 1 }} | ${"b"} | ${"[object Object]b"}
`("returns $expected when $a is added to $b", ({ a, b, expected }) => {
  expect(a + b).toBe(expected);
});
```

> 💡 **참고:** `$value`는 Chai의 `format` 메서드를 통해 처리되며, 출력이 너무 짧게 생략될 경우 `chaiConfig.truncateThreshold` 설정을 늘릴 수 있습니다.

### 🧪 test.for

- **별칭(Alias):** `it.for`

`test.for`는 `test.each`와 유사하지만, 배열을 **스프레드하지 않고 그대로 전달**합니다.  
또한 `TestContext` 객체를 함께 사용할 수 있어 병렬 테스트나 snapshot 테스트에 유리합니다.

#### 기본 배열 예시 (`test.each`와의 비교)

```ts
// `each`는 배열을 인자로 분리 전달
test.each([
  [1, 1, 2],
  [1, 2, 3],
  [2, 1, 3],
])("add(%i, %i) -> %i", (a, b, expected) => {
  expect(a + b).toBe(expected);
});

// `for`는 배열 전체를 하나의 인자로 전달
test.for([
  [1, 1, 2],
  [1, 2, 3],
  [2, 1, 3],
])("add(%i, %i) -> %i", ([a, b, expected]) => {
  expect(a + b).toBe(expected);
});
```

#### TestContext를 활용한 병렬 snapshot 예시

```ts
test.concurrent.for([
  [1, 1],
  [1, 2],
  [2, 1],
])("add(%i, %i)", ([a, b], { expect }) => {
  expect(a + b).matchSnapshot();
});
```

## bench

`bench()`는 성능 벤치마크를 정의하는 함수입니다.  
Vitest는 내부적으로 [`tinybench`](https://github.com/tinylibs/tinybench) 라이브러리를 사용하며, 관련 옵션도 동일하게 지원합니다.

### ✅ 기본 사용법

```ts
import { bench } from "vitest";

bench(
  "일반 정렬",
  () => {
    const x = [1, 5, 4, 2, 3];
    x.sort((a, b) => a - b);
  },
  { time: 1000 }
); // 1000ms 동안 반복 실행
```

옵션 설명:

```ts
interface Options {
  time?: number; // 벤치마크 실행 시간 (기본값: 500ms)
  iterations?: number; // 반복 횟수
  warmupTime?: number; // 워밍업 시간
  warmupIterations?: number; // 워밍업 반복 횟수
  setup?: Hook; // 각 반복 전에 실행할 함수
  teardown?: Hook; // 각 반복 후에 실행할 함수
  throws?: boolean; // 실패 시 예외 발생 여부
}
```

### 실행 결과 예시

```bash
  name               hz         min     max    mean     p75     p99    rme    samples
· normal sorting  6,526,368.12  0.0001  0.3638  0.0002  0.0002  0.0002  ±1.41%   652638
```

### bench.skip

```ts
bench.skip("이 벤치마크는 실행되지 않음", () => {
  // ...
});
```

### bench.only

```ts
bench.only("이 벤치마크만 실행됨", () => {
  // ...
});
```

### bench.todo

```ts
bench.todo("아직 구현되지 않은 벤치마크");
```

## describe

`describe`는 관련 테스트 또는 벤치마크들을 그룹화하기 위한 블록입니다.  
중첩이 가능하며, 가독성과 출력 구조를 명확하게 정리할 수 있습니다.

### ✅ 테스트 그룹화 예시

```ts
import { describe, expect, test } from "vitest";

describe("사용자 객체", () => {
  test("정의되어 있음", () => {
    expect({ name: "Kim" }).toBeDefined();
  });

  test("이름 확인", () => {
    expect("Kim").toContain("K");
  });
});
```

### ✅ 벤치마크 그룹화 예시

```ts
import { bench, describe } from "vitest";

describe("정렬 비교", () => {
  bench("기본 정렬", () => {
    const x = [3, 1, 4, 2];
    x.sort((a, b) => a - b);
  });

  bench("역순 후 정렬", () => {
    const x = [3, 1, 4, 2];
    x.reverse().sort((a, b) => a - b);
  });
});
```

### describe.skip

```ts
describe.skip("이 describe는 실행되지 않음", () => {
  test("이 테스트도 생략됨", () => {
    expect(true).toBe(false);
  });
});
```

### ✅ describe.only

```ts
describe.only("이 describe만 실행됨", () => {
  test("실행됨", () => {
    expect(true).toBe(true);
  });
});
```

### describe.todo

```ts
describe.todo("나중에 구현할 describe");
```

### describe.each

여러 테스트가 동일한 데이터를 기준으로 반복될 경우 사용합니다.

```ts
describe.each([
  { a: 1, b: 2, expected: 3 },
  { a: 2, b: 3, expected: 5 },
])("덧셈 테스트($a + $b)", ({ a, b, expected }) => {
  test(`결과는 ${expected}`, () => {
    expect(a + b).toBe(expected);
  });
});
```

### describe.for

`describe.each`와 유사하나, 배열을 스프레드하지 않고 전달하며 `test.for`와 동일한 문법을 따릅니다.

```ts
describe.for([
  [1, 2, 3],
  [2, 3, 5],
])("덧셈 테스트", ([a, b, expected]) => {
  test("합이 올바른가?", () => {
    expect(a + b).toBe(expected);
  });
});
```

## 테스트 생명주기 훅 (Test Lifecycle Hooks)

각 훅은 `describe` 블록 내에서 사용하거나 파일의 최상위 수준에서 사용할 수 있습니다.  
Promise를 반환하면 해당 Promise가 resolve될 때까지 기다립니다.

### beforeEach

- 각 테스트 실행 **전**에 호출됨

```ts
import { beforeEach, test } from "vitest";

beforeEach(async () => {
  await resetDatabase();
  await addUser({ name: "John" });
});

test("유저가 추가되어 있음", async () => {
  // 위에서 John 추가됨
});
```

- 정리(clean-up) 함수를 리턴하면 `afterEach`처럼 사용 가능

```ts
beforeEach(async () => {
  await prepareSomething();

  return async () => {
    await cleanupSomething();
  };
});
```

### afterEach

- 각 테스트 실행 **후**에 호출됨

```ts
import { afterEach } from "vitest";

afterEach(async () => {
  await clearDatabase();
});
```

### beforeAll

- 모든 테스트가 실행되기 **전 한 번만** 실행

```ts
import { beforeAll } from "vitest";

beforeAll(async () => {
  await connectToDatabase();
});
```

- 정리(clean-up) 함수 리턴 가능 (afterAll처럼 동작)

```ts
beforeAll(async () => {
  await startService();

  return async () => {
    await stopService();
  };
});
```

### afterAll

- 모든 테스트가 실행된 **후 한 번만** 실행

```ts
import { afterAll } from "vitest";

afterAll(async () => {
  await disconnectDatabase();
});
```

## 🧷 실행 중 훅 (Runtime Hooks)

### onTestFinished

- 테스트가 **끝난 직후** 실행
- `afterEach` 이후에 호출됨
- 리소스 해제를 위한 재사용 로직 구현 시 유용

```ts
import { test, onTestFinished } from "vitest";

test("DB 쿼리 테스트", () => {
  const db = connectDb();

  onTestFinished(() => db.close());

  db.query("SELECT * FROM users");
});
```

> 💡 병렬 테스트에서는 `test.concurrent` 내부에서 `onTestFinished`를 context에서 destructure해서 사용해야 함

```ts
test.concurrent("병렬 테스트", ({ onTestFinished }) => {
  const db = connectDb();
  onTestFinished(() => db.close());
});
```

### onTestFailed

- 테스트가 **실패한 경우에만** 호출됨
- 디버깅에 유용하며 `task.result.errors`로 에러 정보 접근 가능

```ts
import { test, onTestFailed } from "vitest";

test("DB 쿼리 실패 테스트", () => {
  const db = connectDb();

  onTestFailed(({ task }) => {
    console.log(task.result.errors);
  });

  db.query("SELECT * FROM invalid_table");
});
```

> 💡 병렬 테스트에서는 반드시 `context`로부터 `onTestFailed`를 destructure해서 사용해야 함

```ts
test.concurrent("병렬 실패 테스트", ({ onTestFailed }) => {
  const db = connectDb();
  onTestFailed(({ task }) => {
    console.log(task.result.errors);
  });
});
```
