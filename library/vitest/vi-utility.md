# Vi

Vitest는 테스트를 도와주는 유틸리티 함수들을 `vi` 헬퍼를 통해 제공합니다.\
`globals` 설정이 활성화되어 있으면 전역에서 접근할 수 있고([globals 설정](/config/#globals) 참고), 그렇지 않으면 `vitest` 패키지에서 직접 import 해야 합니다:

```ts
import { vi } from "vitest";
```

## 모듈 모킹(Mock Modules)

이 섹션은 [모듈 모킹](/guide/mocking#modules)을 할 때 사용할 수 있는 API를 설명합니다.\
Vitest는 `require()`를 통해 import된 모듈을 모킹하는 것을 지원하지 않습니다.

### vi.mock

- **타입**: `(path: string, factory?: MockOptions | ((importOriginal: () => unknown) => unknown)) => void`
- **타입**: `<T>(path: Promise<T>, factory?: MockOptions | ((importOriginal: () => T) => T | Promise<T>)) => void`

주어진 `path`로부터 import된 모든 모듈을 대체합니다.\
path에는 Vite에서 설정한 alias도 사용할 수 있습니다.\
`vi.mock` 호출은 항상 위로 호이스팅(hoisting)되므로, 호출 위치와 관계없이 import보다 먼저 실행됩니다.\
만약 스코프 밖의 변수를 참조해야 한다면 `vi.hoisted`를 사용하여 정의할 수 있습니다.

> ⚠️ 주의  
> `vi.mock`은 `import` 키워드로 가져온 모듈에서만 작동합니다.\
> `require()`로 가져온 모듈은 지원하지 않습니다.
>
> Vitest는 정적으로 파일을 분석하여 `vi.mock`을 호이스팅합니다.\
> 따라서 `vi`가 `vitest`에서 직접 import되지 않은 경우 사용할 수 없습니다.\
> `vi.mock`을 사용하려면 `vitest`에서 직접 import하거나 `globals` 설정을 활성화해야 합니다.
>
> `setupFiles` 내에서 import된 모듈은 테스트 파일이 실행될 때 이미 캐시되었기 때문에 mock 처리되지 않습니다.\
> 이 경우 `vi.resetModules()`를 `vi.hoisted` 내에서 호출해 캐시를 초기화할 수 있습니다.

`factory` 함수가 정의되어 있다면, 해당 모듈을 import할 때마다 factory의 반환값을 사용하게 됩니다.\
factory 함수는 한 번만 호출되며, `vi.unmock` 또는 `vi.doUnmock`를 호출하기 전까지 캐시됩니다.

Jest와는 달리 factory 함수는 비동기(async)로 작성할 수 있습니다.\
`vi.importActual`를 사용하면 원래 모듈의 구현을 가져와 일부만 모킹할 수도 있습니다.

`factory` 대신 `spy` 속성이 있는 객체를 넘길 수도 있습니다.\
`spy: true`일 경우, 해당 모듈은 자동으로 모킹되지만 구현은 유지됩니다.\
즉, 원래 기능은 그대로 작동하지만 호출 여부나 반환값 등을 검증할 수 있습니다.

```ts
import { calculator } from "./src/calculator.ts";

vi.mock("./src/calculator.ts", { spy: true });

// 실제 구현을 호출하지만,
// 나중에 호출 여부를 검증할 수 있음
const result = calculator(1, 2);

expect(result).toBe(3);
expect(calculator).toHaveBeenCalledWith(1, 2);
expect(calculator).toHaveReturned(3);
```

Vitest는 `vi.mock` 또는 `vi.doMock`에서 문자열 대신 모듈 `import()`를 사용하는 것도 지원합니다.\
이 방식은 IDE 지원이 뛰어나며, 모듈 경로가 변경되어도 자동으로 갱신됩니다.\
`importOriginal()`의 타입도 자동 추론됩니다.

```ts
vi.mock(import("./path/to/module.js"), async (importOriginal) => {
  const mod = await importOriginal();
  return {
    ...mod,
    total: vi.fn(), // total만 모킹
  };
});
```

> ⚠️ TypeScript에서 `tsconfig.json`에 `paths` alias를 설정한 경우, 컴파일러가 타입을 제대로 추론하지 못할 수 있습니다.\
> 이를 방지하려면 alias 경로 대신 상대 경로를 사용해야 합니다.

> ⚠️ `vi.mock`은 **호이스팅됨** (즉, 파일의 맨 위로 이동).\
> 따라서 `beforeEach`나 `test` 내부에서 작성해도 import보다 먼저 실행됨.  
> 외부 변수 참조가 필요하면 `vi.doMock` 또는 `vi.hoisted`를 사용해야 함.

```ts
import { namedExport } from "./path/to/module.js";

const mocks = vi.hoisted(() => {
  return {
    namedExport: vi.fn(),
  };
});

vi.mock("./path/to/module.js", () => {
  return {
    namedExport: mocks.namedExport,
  };
});

vi.mocked(namedExport).mockReturnValue(100);

expect(namedExport()).toBe(100);
expect(namedExport).toBe(mocks.namedExport);
```

> ⚠️ `default export`를 모킹할 경우, factory에서 반드시 `default` 키를 제공해야 합니다.

```ts
vi.mock("./path/to/module.js", () => {
  return {
    default: { myDefaultKey: vi.fn() },
    namedExport: vi.fn(),
  };
});
```

`__mocks__` 폴더가 존재할 경우, factory 함수 없이 `vi.mock`을 호출하면 해당 파일을 모듈로 사용합니다.

예를 들어 다음과 같은 구조가 있다면:

```plain
- __mocks__
  - axios.js
- src
  __mocks__
    - increment.js
  - increment.js
- tests
  - increment.test.js
```

다음과 같이 작성하면:

```ts
import { vi } from "vitest";
import axios from "axios";
import { increment } from "../increment.js";

vi.mock("axios");
vi.mock("../increment.js");

axios.get(`/apples/${increment(1)}`);
```

자동으로 `__mocks__/axios.js`와 `src/__mocks__/increment.js`를 불러옵니다.

> ⚠️ `vi.mock`을 호출하지 않으면 모듈은 자동으로 모킹되지 않습니다.\
> Jest의 auto-mocking처럼 사용하려면, [`setupFiles`](/config/#setupfiles) 내에서 명시적으로 `vi.mock`을 호출해야 합니다.

모킹할 파일이나 `__mocks__`가 없을 경우, Vitest는 원본 모듈을 가져와 모든 export를 자동으로 모킹합니다.\
(자동 모킹 알고리즘은 [여기 참고](/guide/mocking#automocking-algorithm))

### vi.doMock

- **타입**: `(path: string, factory?: MockOptions | ((importOriginal: () => unknown) => unknown)) => void`
- **타입**: `<T>(path: Promise<T>, factory?: MockOptions | ((importOriginal: () => T) => T | Promise<T>)) => void`

`vi.mock`과 동일하지만, **호이스팅되지 않음**.\
즉, 파일 스코프에서 정의한 변수를 참조할 수 있습니다.\
이후의 [동적 import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/import)는 모킹된 모듈을 반환합니다.

> ⚠️ `vi.doMock`은 **이미 import된 모듈을 모킹하지 못합니다.**  
> ESM에서 모든 static import는 호이스팅되므로, 아래와 같은 코드는 동작하지 않습니다:

```ts
vi.doMock("./increment.js"); // 이 호출은 실제 import 이후에 실행됨

import { increment } from "./increment.js";
```

#### 예시

```ts [increment.js]
export function increment(number) {
  return number + 1;
}
```

```ts [increment.test.js]
import { beforeEach, test } from "vitest";
import { increment } from "./increment.js";

// vi.doMock 호출 전이므로 실제 구현이 사용됨
increment(1) === 2;

let mockedIncrement = 100;

beforeEach(() => {
  // 외부 변수를 factory에서 사용할 수 있음
  vi.doMock("./increment.js", () => ({
    increment: () => ++mockedIncrement,
  }));
});

test("동적 import된 모듈은 모킹된다", async () => {
  // 이건 여전히 원본
  expect(increment(1)).toBe(2);

  // 동적 import된 것은 모킹된 함수임
  const { increment: mocked } = await import("./increment.js");
  expect(mocked(1)).toBe(101);
  expect(mocked(1)).toBe(102);
  expect(mocked(1)).toBe(103);
});
```

### vi.mocked

- **타입**: `<T>(obj: T, deep?: boolean) => MaybeMockedDeep<T>`
- **타입**: `<T>(obj: T, options?: { partial?: boolean; deep?: boolean }) => MaybePartiallyMockedDeep<T>`

TypeScript용 타입 헬퍼입니다. 전달받은 객체를 그대로 반환하지만, 타입 시스템에 모킹된 객체임을 알려줍니다.

- `partial: true`인 경우, 반환값이 `Partial<T>`라고 간주됩니다.
- 기본적으로는 객체의 1단계 속성만 모킹된 것으로 간주합니다.
- `deep: true`를 전달하면 전체 객체가 모킹된 것으로 간주합니다.

```ts [example.ts]
export function add(x: number, y: number): number {
  return x + y;
}

export function fetchSomething(): Promise<Response> {
  return fetch("https://vitest.dev/");
}
```

```ts [example.test.ts]
import * as example from "./example";

vi.mock("./example");

test("1 + 1 은 10", async () => {
  vi.mocked(example.add).mockReturnValue(10);
  expect(example.add(1, 1)).toBe(10);
});

test("타입 일부만 맞는 값을 반환", async () => {
  vi.mocked(example.fetchSomething).mockResolvedValue(new Response("hello"));
  vi.mocked(example.fetchSomething, { partial: true }).mockResolvedValue({
    ok: false,
  });

  // 아래는 타입 오류 발생 (partial 옵션 없이 구조 불일치)
  // vi.mocked(example.someFn).mockResolvedValue({ ok: false })
});
```

### vi.importActual

- **타입**: `<T>(path: string) => Promise<T>`

모듈을 가져오되, 모킹 여부와 상관없이 **원본 구현 그대로** import합니다. 모듈의 일부만 모킹하고 싶은 경우에 유용합니다.

```ts
// "./example.js" 모듈 전체를 모킹
vi.mock("./example.js", async () => {
  // 원래 "./example.js" 모듈의 실제 구현을 불러옴
  const originalModule = await vi.importActual("./example.js");

  // 원본 모듈 전체를 복사하고, get 함수만 Vitest의 vi.fn()으로 대체
  return {
    ...originalModule,
    get: vi.fn(),
  };
});
```

example.js에 `get`, `post`, `delete` 함수가 있다고 가정하면, `get`만 모킹되고 `post`, `delete`는 원래 구현 그대로 유지됩니다.

### vi.importMock

- **타입**: `<T>(path: string) => Promise<MaybeMockedDeep<T>>`

모듈을 가져오면서 모든 속성(중첩된 속성 포함)을 자동으로 모킹합니다.\
작동 방식은 `vi.mock`과 동일하며, 동일한 자동 모킹 알고리즘을 따릅니다.

### vi.unmock

- **타입**: `(path: string | Promise<Module>) => void`

모듈을 모킹 레지스트리에서 제거합니다. 이후 `import` 호출은 **항상 원본 모듈**을 반환합니다.\
이 호출은 **호이스팅되므로**, `setupFiles`에서 정의된 모듈만 해제됩니다.

### vi.doUnmock

- **타입**: `(path: string | Promise<Module>) => void`

`vi.unmock`과 동일하지만 **호이스팅되지 않음**.\
따라서 이후 import되는 모듈만 원본으로 되돌릴 수 있으며, 이미 import된 모듈에는 영향을 주지 않습니다.

```ts [increment.js]
export function increment(number) {
  return number + 1;
}
```

```ts [increment.test.js]
import { increment } from "./increment.js";

// 아래 모킹은 호이스팅되어 import 전에 실행됨
vi.mock("./increment.js", () => ({ increment: () => 100 }));

increment(1) === 100;
increment(30) === 100;

// 이 호출은 호이스팅되지 않음
vi.doUnmock("./increment.js");

// 여전히 이전의 모킹된 결과를 반환함
increment(1) === 100;

// 새로운 dynamic import는 원본 모듈을 반환함
const { increment: unmockedIncrement } = await import("./increment.js");

unmockedIncrement(1) === 2;
unmockedIncrement(30) === 31;
```

### vi.resetModules

- **타입**: `() => Vitest`

모듈 캐시를 모두 초기화하여, 이후 import 시 모듈이 **다시 평가**되도록 만듭니다. 단, **최상위 import는 다시 평가되지 않습니다.**

이 기능은 각 테스트 간에 모듈 상태가 충돌하지 않도록 격리(isolate)하는 데 유용합니다.

```ts
import { vi } from "vitest";

import { data } from "./data.js"; // 이 import는 resetModules 이후에도 재평가되지 않음

beforeEach(() => {
  vi.resetModules();
});

test("상태 변경 테스트", async () => {
  const mod = await import("./some/path.js"); // 이 모듈은 매번 새로 import됨
  mod.changeLocalState("new value");
  expect(mod.getLocalState()).toBe("new value");
});

test("초기 상태 테스트", async () => {
  const mod = await import("./some/path.js");
  expect(mod.getLocalState()).toBe("old value");
});
```

> ⚠️ 주의: 모듈 캐시는 초기화되지만, **mock 레지스트리는 초기화되지 않습니다.**  
> 모킹을 초기화하려면 [`vi.unmock`](#vi-unmock) 또는 [`vi.doUnmock`](#vi-dounmock)를 사용하세요.

### vi.dynamicImportSettled

- **타입**: `() => Promise<void>`

모든 동적 import가 완료될 때까지 기다립니다. 동기 호출로 인해 시작된 동적 import를 기다릴 수 없는 상황에서 유용합니다.

```ts
function renderComponent() {
  import("./component.js").then(({ render }) => {
    render();
  });
}

test("컴포넌트가 렌더링됨", async () => {
  renderComponent();
  await vi.dynamicImportSettled();
  expect(document.querySelector(".component")).not.toBeNull();
});
```

> 💡 팁: 동적 import 내부에서 또 다른 동적 import가 발생해도 모두 대기합니다.  
> 또한 import가 끝난 다음 타이머 큐의 다음 tick까지 기다립니다.

## 함수 및 객체 모킹

Vitest는 [함수 모킹](/api/mock) 및 글로벌/환경 객체의 재정의도 지원합니다.

### vi.fn

- **타입**: `(fn?: Function) => Mock`

스파이 함수(mock 함수)를 생성합니다. 함수가 호출될 때마다 인자, 반환값, 호출 횟수 등을 기록합니다.

```ts
const getApples = vi.fn(() => 0);

getApples();

expect(getApples).toHaveBeenCalled();
expect(getApples).toHaveReturnedWith(0);

getApples.mockReturnValueOnce(5);

const res = getApples();
expect(res).toBe(5);
expect(getApples).toHaveNthReturnedWith(2, 5);
```

### vi.mockObject <버전>3.2.0</버전>

- **타입**: `<T>(value: T) => MaybeMockedDeep<T>`

객체의 속성과 메서드를 자동으로 깊게(deep) 모킹합니다. `vi.mock()`으로 모듈 export를 모킹하는 것과 유사합니다.

```ts
const original = {
  simple: () => "value",
  nested: {
    method: () => "real",
  },
  prop: "foo",
};

const mocked = vi.mockObject(original);

expect(mocked.simple()).toBe(undefined);
expect(mocked.nested.method()).toBe(undefined);
expect(mocked.prop).toBe("foo");

mocked.simple.mockReturnValue("mocked");
mocked.nested.method.mockReturnValue("mocked nested");

expect(mocked.simple()).toBe("mocked");
expect(mocked.nested.method()).toBe("mocked nested");
```

### vi.isMockFunction

- **타입**: `(fn: Function) => boolean`

해당 함수가 모킹된 함수인지 확인합니다. TypeScript에서는 타입 추론도 도와줍니다.

### vi.clearAllMocks

등록된 모든 mock 함수에 대해 `.mockClear()`를 호출합니다.\
→ 기록은 지우되, 구현은 유지됩니다.

### vi.resetAllMocks

등록된 모든 mock 함수에 대해 `.mockReset()`을 호출합니다.\
→ 기록을 지우고, 구현도 초기화됩니다.

### vi.restoreAllMocks

등록된 모든 mock 함수에 대해 `.mockRestore()`를 호출합니다.\
→ 기록, 구현, 프로퍼티 정의까지 원래 상태로 복원됩니다.

### vi.spyOn

- **타입**: `<T, K extends keyof T>(object: T, method: K, accessType?: 'get' | 'set') => MockInstance`

객체의 메서드 또는 getter/setter에 대해 스파이를 생성합니다.\
`vi.fn()`과 유사하며, 원본 메서드를 감싸서 추적 가능합니다.

```ts
let apples = 0;
const cart = {
  getApples: () => 42,
};

const spy = vi.spyOn(cart, "getApples").mockImplementation(() => apples);
apples = 1;

expect(cart.getApples()).toBe(1);

expect(spy).toHaveBeenCalled();
expect(spy).toHaveReturnedWith(1);
```

> 💡 `using` 키워드(Explicit Resource Management proposal)를 사용하는 환경에서는
> 블록을 빠져나갈 때 자동으로 `mockRestore()`를 호출할 수 있습니다:

```ts
it('console.log 호출 여부 테스트', () => {
  using spy = vi.spyOn(console, 'log').mockImplementation(() => {})
  console.log('message')
  expect(spy).toHaveBeenCalled()
})
// 여기서 console.log가 원복됨
```

> 💡 `afterEach()`에서 `vi.restoreAllMocks`를 호출하거나
> `test.restoreMocks` 설정을 통해 자동 복원할 수 있습니다.

### vi.stubEnv {#vi-stubenv}

- **타입**: `<T extends string>(name: T, value: T extends "PROD" | "DEV" | "SSR" ? boolean : string | undefined) => Vitest`

`process.env` 및 `import.meta.env`에 설정된 환경 변수의 값을 변경합니다.  
기존 값을 복원하려면 `vi.unstubAllEnvs()`를 호출하면 됩니다.

```ts
import { vi } from "vitest";

// 기본값은 'development'
vi.stubEnv("NODE_ENV", "production");

process.env.NODE_ENV === "production";
import.meta.env.NODE_ENV === "production";

// 다시 undefined로 설정
vi.stubEnv("NODE_ENV", undefined);

process.env.NODE_ENV === undefined;
import.meta.env.NODE_ENV === undefined;
```

> 💡 `import.meta.env.MODE = 'test'`처럼 직접 값을 할당해도 되지만,  
> 이 경우 `vi.unstubAllEnvs()`로는 복구할 수 없습니다.

### vi.unstubAllEnvs {#vi-unstuballenvs}

- **타입**: `() => Vitest`

`vi.stubEnv()`로 변경된 모든 환경 변수 값을 원래대로 되돌립니다.

```ts
import { vi } from "vitest";

// 변경 전 값은 'development'
vi.stubEnv("NODE_ENV", "production");

vi.stubEnv("NODE_ENV", "staging");

vi.unstubAllEnvs();

process.env.NODE_ENV === "development";
import.meta.env.NODE_ENV === "development";
```

### vi.stubGlobal

- **타입**: `(name: string | number | symbol, value: unknown) => Vitest`

`globalThis`(또는 jsdom, happy-dom 환경에서는 `window`)에 존재하는 전역 변수의 값을 변경합니다.  
기존 값을 복구하려면 `vi.unstubAllGlobals()`를 호출하면 됩니다.

```ts
import { vi } from "vitest";

vi.stubGlobal("innerWidth", 100);

innerWidth === 100;
globalThis.innerWidth === 100;
window.innerWidth === 100; // jsdom/happy-dom에서
```

> 💡 전역 값을 직접 할당해도 되지만,  
> 이 경우 `vi.unstubAllGlobals()`로 복구할 수 없습니다.

### vi.unstubAllGlobals {#vi-unstuballglobals}

- **타입**: `() => Vitest`

`vi.stubGlobal()`로 변경된 모든 전역 변수 값을 원래대로 복원합니다.

```ts
import { vi } from "vitest";

const Mock = vi.fn();

vi.stubGlobal("IntersectionObserver", Mock);

IntersectionObserver === Mock;
globalThis.IntersectionObserver === Mock;
window.IntersectionObserver === Mock;

vi.unstubAllGlobals();

globalThis.IntersectionObserver === undefined;
"IntersectionObserver" in globalThis === false;
// 정의되지 않았기 때문에 ReferenceError 발생
IntersectionObserver === undefined;
```

## 가짜 타이머(Fake Timers)

Vitest는 `setTimeout`, `setInterval` 등을 포함한 타이머 API를 가짜로 대체해 제어할 수 있습니다.

### vi.advanceTimersByTime

- **타입**: `(ms: number) => Vitest`

지정된 시간(ms) 동안의 모든 타이머를 즉시 실행시킵니다.\
실행된 타이머가 없거나 지정된 시간에 도달하면 종료됩니다.

```ts
let i = 0;
setInterval(() => console.log(++i), 50);

vi.advanceTimersByTime(150);

// 출력: 1, 2, 3
```

### vi.advanceTimersByTimeAsync

- **타입**: `(ms: number) => Promise<Vitest>`

비동기 타이머를 포함하여, `ms` 밀리초 동안의 타이머를 실행합니다.

```ts
let i = 0;
setInterval(() => Promise.resolve().then(() => console.log(++i)), 50);

await vi.advanceTimersByTimeAsync(150);

// 출력: 1, 2, 3
```

### vi.advanceTimersToNextTimer

- **타입**: `() => Vitest`

대기 중인 **다음 타이머 1개**를 즉시 실행합니다.  
타이머 간 단계별 검증에 유용합니다.

```ts
let i = 0;
setInterval(() => console.log(++i), 50);

vi.advanceTimersToNextTimer(); // 1
vi.advanceTimersToNextTimer(); // 2
vi.advanceTimersToNextTimer(); // 3
```

### vi.advanceTimersToNextTimerAsync

- **타입**: `() => Promise<Vitest>`

`await` 가능한 비동기 타이머도 포함하여 다음 타이머를 실행합니다.

```ts
let i = 0;
setInterval(() => Promise.resolve().then(() => console.log(++i)), 50);

await vi.advanceTimersToNextTimerAsync();
expect(console.log).toHaveBeenCalledWith(1);

await vi.advanceTimersToNextTimerAsync(); // 2
await vi.advanceTimersToNextTimerAsync(); // 3
```

### vi.advanceTimersToNextFrame <버전>2.1.0</버전>

- **타입**: `() => Vitest`

`requestAnimationFrame`에 등록된 콜백을 실행합니다.

```ts
let frameRendered = false;

requestAnimationFrame(() => {
  frameRendered = true;
});

vi.advanceTimersToNextFrame();

expect(frameRendered).toBe(true);
```

### vi.getTimerCount

- **타입**: `() => number`

현재 대기 중인 타이머의 개수를 반환합니다.

### vi.clearAllTimers

등록된 모든 타이머를 제거합니다.  
→ 제거된 타이머는 더 이상 실행되지 않습니다.

### vi.getMockedSystemTime

- **타입**: `() => Date | null`

모킹된 현재 시간을 반환합니다.  
가짜 시간이 설정되지 않은 경우 `null`을 반환합니다.

### vi.getRealSystemTime

- **타입**: `() => number`

`vi.useFakeTimers()`를 사용하고 있을 때에도 실제 시스템 시간을 밀리초(ms) 단위로 반환합니다.

### vi.runAllTicks

- **타입**: `() => Vitest`

`process.nextTick()`으로 예약된 모든 마이크로태스크를 실행합니다.  
→ 마이크로태스크 내에서 다시 예약된 마이크로태스크도 모두 실행됩니다.

### vi.runAllTimers

- **타입**: `() => Vitest`

모든 타이머를 실행하여 타이머 큐가 빌 때까지 반복합니다.  
무한 루프(interval)가 존재하는 경우 10,000회 이후 예외를 던집니다.  
(`fakeTimers.loopLimit` 설정으로 변경 가능)

```ts
let i = 0;
setTimeout(() => console.log(++i));
const interval = setInterval(() => {
  console.log(++i);
  if (i === 3) {
    clearInterval(interval);
  }
}, 50);

vi.runAllTimers();
// 출력: 1, 2, 3
```

### vi.runAllTimersAsync

- **타입**: `() => Promise<Vitest>`

비동기 타이머를 포함하여 모든 타이머를 실행합니다.

```ts
setTimeout(async () => {
  console.log(await Promise.resolve("result"));
}, 100);

await vi.runAllTimersAsync();
// 출력: result
```

### vi.runOnlyPendingTimers

- **타입**: `() => Vitest`

`vi.useFakeTimers()` 호출 후 예약된 타이머만 실행합니다.  
→ 실행 중 새로 예약된 타이머는 실행되지 않습니다.

```ts
let i = 0;
setInterval(() => console.log(++i), 50);

vi.runOnlyPendingTimers();
// 출력: 1
```

### vi.runOnlyPendingTimersAsync

- **타입**: `() => Promise<Vitest>`

`vi.useFakeTimers()` 이후 예약된 비동기 타이머만 실행합니다.\
→ 실행 도중 새로 예약된 타이머는 실행되지 않습니다.

```ts
setTimeout(() => {
  console.log(1);
}, 100);

setTimeout(() => {
  Promise.resolve().then(() => {
    console.log(2);
    setInterval(() => {
      console.log(3);
    }, 40);
  });
}, 10);

await vi.runOnlyPendingTimersAsync();
// 출력 순서: 2, 3, 3, 1
```

### vi.setSystemTime

- **타입**: `(date: string | number | Date) => void`

시스템 시간을 변경합니다.\
가짜 타이머가 활성화된 경우 `Date.now()`, `performance.now()` 등 시간 관련 API에 영향을 줍니다.

```ts
const date = new Date(1998, 11, 19);

vi.useFakeTimers();
vi.setSystemTime(date);

expect(Date.now()).toBe(date.valueOf());

vi.useRealTimers();
```

### vi.useFakeTimers

- **타입**: `(config?: FakeTimerInstallOpts) => Vitest`

테스트 코드는 동기적으로 실행되기 때문에 비동기 함수가 실행되기 전에 단언이 실행되는 문제가 발생합니다.\
그래서 가짜 타이머를 활성화하여 시간 지연 없이 실행되도록 합니다.

이후 `setTimeout`, `setInterval`, `clearTimeout`, `clearInterval`, `Date` 등은 전부 모킹된 버전으로 대체됩니다.\
즉, `useFakeTimers` 이후 `setTimeout`, `setInterval`, `clearTimeout`, `clearInterval`, `Date` 등은 호출해도 아무 일도 일어나지 않습니다.

`useFakeTimers()`는 시간을 멈춘 세상을 만드는 것입니다.\
이후 `advanceTimersByTime()` 같은 함수로 멈춘 세상에서 임의로 시간을 흘리는, 즉시 시간 이동 시뮬레이션이 가능합니다.

> 💡 `nextTick`, `queueMicrotask`도 포함하고 싶다면 아래처럼 사용합니다:

```ts
vi.useFakeTimers({
  toFake: ["nextTick", "queueMicrotask"],
});
```

> ⚠️ `node:child_process` 환경에서 `nextTick` 모킹은 지원되지 않음.\
> 대신 `--pool=threads` 옵션으로 실행 시 가능.

### vi.isFakeTimers

- **타입**: `() => boolean`

현재 가짜 타이머가 활성화되어 있는지 여부를 반환합니다.

### vi.useRealTimers

- **타입**: `() => Vitest`

가짜 타이머를 비활성화하고 원래 타이머 구현으로 되돌립니다.\
이전에 예약된 모든 타이머는 삭제됩니다.

```tsx
beforeEach(() => {
  vi.useFakeTimers();
});

afterEach(() => {
  vi.useRealTimers();
});
```

## 비동기 상태 대기

### vi.waitFor {#vi-waitfor}

- **타입**: `<T>(callback: WaitForCallback<T>, options?: number | WaitForOptions) => Promise<T>`

콜백이 성공할 때까지 기다립니다.\
콜백이 에러를 throw 하거나 reject될 경우, 지정된 시간 동안 재시도합니다.

- `options`에 숫자를 지정하면 timeout(ms)으로 간주됩니다.
- 기본값: `{ timeout: 1000, interval: 50 }`

```ts
test("서버가 준비되면 통과", async () => {
  const server = createServer();

  await vi.waitFor(
    () => {
      if (!server.isReady) throw new Error("Server not started");
      console.log("Server started");
    },
    {
      timeout: 500,
      interval: 20,
    }
  );

  expect(server.isReady).toBe(true);
});
```

> 💡 `vi.useFakeTimers()`가 활성화되어 있을 경우,  
> `vi.waitFor()`는 내부적으로 `vi.advanceTimersByTime(interval)`을 호출합니다.

### vi.waitUntil {#vi-waituntil}

- **타입**: `<T>(callback: WaitUntilCallback<T>, options?: number | WaitUntilOptions) => Promise<T>`

콜백의 반환값이 truthy일 때까지 기다립니다.\
콜백에서 예외가 발생하면 즉시 중단됩니다.\
존재 여부 확인에 유용합니다.

```ts
test("요소가 DOM에 존재함", async () => {
  const element = await vi.waitUntil(() => document.querySelector(".element"), {
    timeout: 500,
    interval: 20,
  });

  expect(element.querySelector(".element-child")).toBeTruthy();
});
```

### vi.hoisted {#vi-hoisted}

- **타입**: `<T>(factory: () => T) => T`

모듈 import보다 먼저 실행되어야 하는 코드를 등록합니다.\
기본적으로 `vi.mock()`이 호이스팅되기 때문에, 외부 변수 참조는 제한됩니다.\
`vi.hoisted`를 사용하면 변수를 먼저 정의하고 `vi.mock` 안에서 사용할 수 있습니다.

```ts
import { originalMethod } from "./path/to/module.js";

const { mockedMethod } = vi.hoisted(() => {
  return { mockedMethod: vi.fn() };
});

vi.mock("./path/to/module.js", () => {
  return { originalMethod: mockedMethod };
});

mockedMethod.mockReturnValue(100);
expect(originalMethod()).toBe(100);
```

> ⚠️ `vi.hoisted` 내부에서는 import된 변수 사용 불가.\
> 필요한 경우 `await import()`로 동적 import를 사용해야 함.

### vi.setConfig

- **타입**: `RuntimeConfig`

현재 테스트 파일의 설정을 동적으로 변경합니다.

```ts
vi.setConfig({
  allowOnly: true,
  testTimeout: 10_000,
  hookTimeout: 10_000,
  clearMocks: true,
  restoreMocks: true,
  fakeTimers: {
    now: new Date(2021, 11, 19),
  },
  maxConcurrency: 10,
  sequence: {
    hooks: "stack",
  },
});
```

### vi.resetConfig

- **타입**: `RuntimeConfig`

`vi.setConfig()` 호출 전의 원래 상태로 설정을 복원합니다.
