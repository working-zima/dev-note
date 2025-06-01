---
title: 테스트
description: 테스트 작성하기
nav: 8
---

## 테스트 환경 설정

### 테스트 러너(Test Runners)

일반적으로, 테스트 러너는 JavaScript/TypeScript 구문을 실행할 수 있도록 설정해야 합니다. UI 컴포넌트를 테스트하려는 경우, 테스트 러너가 JSDOM을 사용하여 가짜 DOM 환경(mock DOM environment)을 제공하도록 설정해야 할 가능성이 높습니다.

테스트 러너 설정에 대한 자세한 내용은 아래 리소스를 참고하십시오:

- **Jest**

  - [Jest: 시작하기](https://jestjs.io/docs/getting-started)
  - [Jest: 설정 - 테스트 환경](https://jestjs.io/docs/configuration#testenvironment-string)

- **Vitest**

  - [Vitest: 시작하기](https://vitest.dev/guide)
  - [Vitest: 설정 - 테스트 환경](https://vitest.dev/config/#environment)

### UI 및 네트워크 테스트 도구

**Zustand에 연결된 React 컴포넌트를 테스트하기 위해 [React Testing Library (RTL)](https://testing-library.com/docs/react-testing-library/intro) 사용을 권장합니다.** RTL은 ReactDOM의 `render` 함수와 `react-dom/tests-utils`의 `act`를 활용하는, 단순하고 완전한 React DOM 테스트 유틸리티로서 좋은 테스트 관행을 장려합니다. 또한 [Native Testing Library (RNTL)](https://testing-library.com/docs/react-native-testing-library/intro)은 React Native 컴포넌트를 테스트하기 위한 RTL의 대안입니다. [Testing Library](https://testing-library.com/) 도구 모음은 이외에도 다양한 인기 프레임워크용 어댑터를 포함합니다.

네트워크 요청을 모킹할 때는 [Mock Service Worker (MSW)](https://mswjs.io/) 사용을 권장합니다. 이 도구를 사용하면 애플리케이션 로직을 테스트 시점에 변경하거나 모킹할 필요가 없습니다.

- **React Testing Library (DOM)**

  - [DOM Testing Library: 설정](https://testing-library.com/docs/dom-testing-library/setup)
  - [React Testing Library: 설정](https://testing-library.com/docs/react-testing-library/setup)
  - [Testing Library Jest-DOM Matchers](https://testing-library.com/docs/ecosystem-jest-dom)

- **Native Testing Library (React Native)**

  - [Native Testing Library: 설정](https://testing-library.com/docs/react-native-testing-library/setup)

- **User Event Testing Library (DOM)**

  - [User Event Testing Library: 설정](https://testing-library.com/docs/user-event/setup)

- **TypeScript + Jest**

  - [TypeScript for Jest: 설정](https://kulshekhar.github.io/ts-jest/docs/getting-started/installation)

- **TypeScript + Node**

  - [TypeScript for Node: 설정](https://typestrong.org/ts-node/docs/installation)

- **Mock Service Worker**

  - [MSW: 설치](https://mswjs.io/docs/getting-started/install)
  - [MSW: Mock 요청 설정](https://mswjs.io/docs/getting-started/mocks/rest-api)
  - [MSW: Node 환경 통합](https://mswjs.io/docs/getting-started/integrate/node)

## Zustand 테스트 설정하기

> **참고**: Jest와 Vitest는 약간의 차이가 있으며, Vitest는 **ES 모듈**, Jest는 **CommonJS 모듈**을 사용합니다. Vitest를 사용할 경우 이 점을 유의해야 합니다.

아래의 mock 코드는 각 테스트가 종료된 후 zustand store를 초기 상태로 리셋할 수 있도록 설정합니다.

...

아래는 테스트 전용 공통 코드를 포함한 예제입니다. 이 코드는 `Context` API를 사용한 구현과 그렇지 않은 구현에서 동일한 store 생성 함수를 재사용할 수 있도록 작성되었습니다.

```ts
// shared/counter-store-creator.ts
import { type StateCreator } from "zustand";

export type CounterStore = {
  count: number;
  inc: () => void;
};

export const counterStoreCreator: StateCreator<CounterStore> = (set) => ({
  count: 1,
  inc: () => set((state) => ({ count: state.count + 1 })),
});
```

### Jest 환경 설정

다음은 Jest 환경에서 Zustand를 모킹(mock)하기 위한 설정입니다.

```ts
// __mocks__/zustand.ts
import { act } from "@testing-library/react";
import type * as ZustandExportedTypes from "zustand";
export * from "zustand";

const { create: actualCreate, createStore: actualCreateStore } =
  jest.requireActual<typeof ZustandExportedTypes>("zustand");

// 애플리케이션 전반에서 선언된 모든 store에 대해 초기화 함수를 보관할 변수
export const storeResetFns = new Set<() => void>();

const createUncurried = <T>(
  stateCreator: ZustandExportedTypes.StateCreator<T>
) => {
  const store = actualCreate(stateCreator);
  const initialState = store.getInitialState();
  storeResetFns.add(() => {
    store.setState(initialState, true);
  });
  return store;
};

// store를 생성할 때 초기 상태를 저장하고 reset 함수를 등록함
export const create = (<T>(
  stateCreator: ZustandExportedTypes.StateCreator<T>
) => {
  console.log("zustand create mock");
  return typeof stateCreator === "function"
    ? createUncurried(stateCreator)
    : createUncurried;
}) as typeof ZustandExportedTypes.create;

const createStoreUncurried = <T>(
  stateCreator: ZustandExportedTypes.StateCreator<T>
) => {
  const store = actualCreateStore(stateCreator);
  const initialState = store.getInitialState();
  storeResetFns.add(() => {
    store.setState(initialState, true);
  });
  return store;
};

export const createStore = (<T>(
  stateCreator: ZustandExportedTypes.StateCreator<T>
) => {
  console.log("zustand createStore mock");
  return typeof stateCreator === "function"
    ? createStoreUncurried(stateCreator)
    : createStoreUncurried;
}) as typeof ZustandExportedTypes.createStore;

// 각 테스트 후 모든 store 초기화
afterEach(() => {
  act(() => {
    storeResetFns.forEach((resetFn) => {
      resetFn();
    });
  });
});
```

```ts
// setup-jest.ts
import "@testing-library/jest-dom";
```

```ts
// jest.config.ts
import type { JestConfigWithTsJest } from "ts-jest";

const config: JestConfigWithTsJest = {
  preset: "ts-jest",
  testEnvironment: "jsdom",
  setupFilesAfterEnv: ["./setup-jest.ts"],
};

export default config;
```

> **참고**: TypeScript를 사용할 경우 `ts-jest`와 `ts-node` 패키지를 설치해야 합니다.

...(이전 내용 생략)...

### Vitest 환경 설정

다음은 Vitest 환경에서 Zustand를 모킹하기 위한 설정입니다.

> ⚠️ **주의:** Vitest에서는 [root 설정](https://vitest.dev/config/#root)을 변경할 수 있습니다.
> 따라서 `__mocks__` 디렉터리를 올바른 위치에 생성해야 합니다.
> 예를 들어 root가 `./src`로 설정되어 있다면, `__mocks__` 디렉터리는 `./src/__mocks__`에 있어야 하며, `./__mocks__`는 잘못된 위치입니다. 위치가 잘못되면 Vitest에서 모킹이 제대로 작동하지 않을 수 있습니다.

```ts
// __mocks__/zustand.ts
import { act } from "@testing-library/react";
import type * as ZustandExportedTypes from "zustand";
export * from "zustand";

const { create: actualCreate, createStore: actualCreateStore } =
  await vi.importActual<typeof ZustandExportedTypes>("zustand");

// 애플리케이션 전반에서 선언된 모든 store에 대해 초기화 함수를 보관할 변수
export const storeResetFns = new Set<() => void>();

const createUncurried = <T>(
  stateCreator: ZustandExportedTypes.StateCreator<T>
) => {
  const store = actualCreate(stateCreator);
  const initialState = store.getInitialState();
  storeResetFns.add(() => {
    store.setState(initialState, true);
  });
  return store;
};

// store를 생성할 때 초기 상태를 저장하고 reset 함수를 등록함
export const create = (<T>(
  stateCreator: ZustandExportedTypes.StateCreator<T>
) => {
  console.log("zustand create mock");
  return typeof stateCreator === "function"
    ? createUncurried(stateCreator)
    : createUncurried;
}) as typeof ZustandExportedTypes.create;

const createStoreUncurried = <T>(
  stateCreator: ZustandExportedTypes.StateCreator<T>
) => {
  const store = actualCreateStore(stateCreator);
  const initialState = store.getInitialState();
  storeResetFns.add(() => {
    store.setState(initialState, true);
  });
  return store;
};

export const createStore = (<T>(
  stateCreator: ZustandExportedTypes.StateCreator<T>
) => {
  console.log("zustand createStore mock");
  return typeof stateCreator === "function"
    ? createStoreUncurried(stateCreator)
    : createStoreUncurried;
}) as typeof ZustandExportedTypes.createStore;

// 각 테스트 후 모든 store 초기화
afterEach(() => {
  act(() => {
    storeResetFns.forEach((resetFn) => {
      resetFn();
    });
  });
});
```

> **참고**: [globals 설정](https://vitest.dev/config/#globals)이 비활성화된 경우, `import { afterEach, vi } from 'vitest'`를 파일 상단에 추가해야 합니다.

```ts
// global.d.ts
/// <reference types="vite/client" />
/// <reference types="vitest/globals" />
```

> **참고**: globals 설정을 비활성화한 경우에는 위 참조 타입 중 `/// <reference types="vitest/globals" />`를 제거해야 할 수도 있습니다.

```ts
// setup-vitest.ts
import "@testing-library/jest-dom";

vi.mock("zustand"); // Jest처럼 자동 모킹 적용
```

> **참고**: globals 설정이 비활성화된 경우 `import { vi } from 'vitest'`를 상단에 추가해야 합니다.

```ts
// vitest.config.ts
import { defineConfig, mergeConfig } from "vitest/config";
import viteConfig from "./vite.config";

export default defineConfig((configEnv) =>
  mergeConfig(
    viteConfig(configEnv),
    defineConfig({
      test: {
        globals: true,
        environment: "jsdom",
        setupFiles: ["./setup-vitest.ts"],
      },
    })
  )
);
```

### 컴포넌트 테스트

다음 예제에서는 `useCounterStore`를 사용합니다.

> **참고**: 이 예제는 모두 TypeScript로 작성되어 있습니다.

```ts
// shared/counter-store-creator.ts
import { type StateCreator } from "zustand";

export type CounterStore = {
  count: number;
  inc: () => void;
};

export const counterStoreCreator: StateCreator<CounterStore> = (set) => ({
  count: 1,
  inc: () => set((state) => ({ count: state.count + 1 })),
});
```

```ts
// stores/use-counter-store.ts
import { create } from "zustand";

import {
  type CounterStore,
  counterStoreCreator,
} from "../shared/counter-store-creator";

export const useCounterStore = create<CounterStore>()(counterStoreCreator);
```

```tsx
// contexts/use-counter-store-context.tsx
import { type ReactNode, createContext, useContext, useRef } from "react";
import { createStore } from "zustand";
import { useStoreWithEqualityFn } from "zustand/traditional";
import { shallow } from "zustand/shallow";

import {
  type CounterStore,
  counterStoreCreator,
} from "../shared/counter-store-creator";

export const createCounterStore = () => {
  return createStore<CounterStore>(counterStoreCreator);
};

export type CounterStoreApi = ReturnType<typeof createCounterStore>;

export const CounterStoreContext = createContext<CounterStoreApi | undefined>(
  undefined
);

export interface CounterStoreProviderProps {
  children: ReactNode;
}

export const CounterStoreProvider = ({
  children,
}: CounterStoreProviderProps) => {
  const counterStoreRef = useRef<CounterStoreApi>(null);
  if (!counterStoreRef.current) {
    counterStoreRef.current = createCounterStore();
  }

  return (
    <CounterStoreContext.Provider value={counterStoreRef.current}>
      {children}
    </CounterStoreContext.Provider>
  );
};

export type UseCounterStoreContextSelector<T> = (store: CounterStore) => T;

export const useCounterStoreContext = <T,>(
  selector: UseCounterStoreContextSelector<T>
): T => {
  const counterStoreContext = useContext(CounterStoreContext);

  if (counterStoreContext === undefined) {
    throw new Error(
      "useCounterStoreContext must be used within CounterStoreProvider"
    );
  }

  return useStoreWithEqualityFn(counterStoreContext, selector, shallow);
};
```

```tsx
// components/counter/counter.tsx
import { useCounterStore } from "../../stores/use-counter-store";

export function Counter() {
  const { count, inc } = useCounterStore();

  return (
    <div>
      <h2>Counter Store</h2>
      <h4>{count}</h4>
      <button onClick={inc}>One Up</button>
    </div>
  );
}
```

```ts
// components/counter/index.ts
export * from "./counter";
```

```tsx
// components/counter/counter.test.tsx
import { act, render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

import { Counter } from "./counter";

describe("Counter", () => {
  test("should render with initial state of 1", async () => {
    renderCounter();

    expect(await screen.findByText(/^1$/)).toBeInTheDocument();
    expect(
      await screen.findByRole("button", { name: /one up/i })
    ).toBeInTheDocument();
  });

  test("should increase count by clicking a button", async () => {
    const user = userEvent.setup();

    renderCounter();

    expect(await screen.findByText(/^1$/)).toBeInTheDocument();

    await user.click(await screen.findByRole("button", { name: /one up/i }));

    expect(await screen.findByText(/^2$/)).toBeInTheDocument();
  });
});

const renderCounter = () => {
  return render(<Counter />);
};
```

```tsx
// components/counter-with-context/counter-with-context.tsx
import {
  CounterStoreProvider,
  useCounterStoreContext,
} from "../../contexts/use-counter-store-context";

const Counter = () => {
  const { count, inc } = useCounterStoreContext((state) => state);

  return (
    <div>
      <h2>Counter Store Context</h2>
      <h4>{count}</h4>
      <button onClick={inc}>One Up</button>
    </div>
  );
};

export const CounterWithContext = () => {
  return (
    <CounterStoreProvider>
      <Counter />
    </CounterStoreProvider>
  );
};
```

```ts
// components/counter-with-context/index.ts
export * from "./counter-with-context";
```

```tsx
// components/counter-with-context/counter-with-context.test.tsx
import { act, render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

import { CounterWithContext } from "./counter-with-context";

describe("CounterWithContext", () => {
  test("should render with initial state of 1", async () => {
    renderCounterWithContext();

    expect(await screen.findByText(/^1$/)).toBeInTheDocument();
    expect(
      await screen.findByRole("button", { name: /one up/i })
    ).toBeInTheDocument();
  });

  test("should increase count by clicking a button", async () => {
    const user = userEvent.setup();

    renderCounterWithContext();

    expect(await screen.findByText(/^1$/)).toBeInTheDocument();

    await user.click(await screen.findByRole("button", { name: /one up/i }));

    expect(await screen.findByText(/^2$/)).toBeInTheDocument();
  });
});

const renderCounterWithContext = () => {
  return render(<CounterWithContext />);
};
```

...(이전 내용 생략)...

> **참고**: [globals 설정](https://vitest.dev/config/#globals)이 비활성화된 경우, 각 테스트 파일 상단에 `import { describe, test, expect } from 'vitest'`를 명시적으로 추가해야 합니다.

## CodeSandbox 데모

- **Jest 데모**: [https://stackblitz.com/edit/jest-zustand](https://stackblitz.com/edit/jest-zustand)
- **Vitest 데모**: [https://stackblitz.com/edit/vitest-zustand](https://stackblitz.com/edit/vitest-zustand)

## Store 테스트하기

다음 예제에서는 `useCounterStore`를 사용합니다.

> **참고**: 이 예제는 모두 TypeScript로 작성되어 있습니다.

```ts
// shared/counter-store-creator.ts
import { type StateCreator } from "zustand";

export type CounterStore = {
  count: number;
  inc: () => void;
};

export const counterStoreCreator: StateCreator<CounterStore> = (set) => ({
  count: 1,
  inc: () => set((state) => ({ count: state.count + 1 })),
});
```

```ts
// stores/use-counter-store.ts
import { create } from "zustand";

import {
  type CounterStore,
  counterStoreCreator,
} from "../shared/counter-store-creator";

export const useCounterStore = create<CounterStore>()(counterStoreCreator);
```

```tsx
// contexts/use-counter-store-context.tsx
import { type ReactNode, createContext, useContext, useRef } from "react";
import { createStore } from "zustand";
import { useStoreWithEqualityFn } from "zustand/traditional";
import { shallow } from "zustand/shallow";

import {
  type CounterStore,
  counterStoreCreator,
} from "../shared/counter-store-creator";

export const createCounterStore = () => {
  return createStore<CounterStore>(counterStoreCreator);
};

export type CounterStoreApi = ReturnType<typeof createCounterStore>;

export const CounterStoreContext = createContext<CounterStoreApi | undefined>(
  undefined
);

export interface CounterStoreProviderProps {
  children: ReactNode;
}

export const CounterStoreProvider = ({
  children,
}: CounterStoreProviderProps) => {
  const counterStoreRef = useRef<CounterStoreApi>(null);
  if (!counterStoreRef.current) {
    counterStoreRef.current = createCounterStore();
  }

  return (
    <CounterStoreContext.Provider value={counterStoreRef.current}>
      {children}
    </CounterStoreContext.Provider>
  );
};

export type UseCounterStoreContextSelector<T> = (store: CounterStore) => T;

export const useCounterStoreContext = <T,>(
  selector: UseCounterStoreContextSelector<T>
): T => {
  const counterStoreContext = useContext(CounterStoreContext);

  if (counterStoreContext === undefined) {
    throw new Error(
      "useCounterStoreContext must be used within CounterStoreProvider"
    );
  }

  return useStoreWithEqualityFn(counterStoreContext, selector, shallow);
};
```

```tsx
// components/counter/counter.tsx
import { useCounterStore } from "../../stores/use-counter-store";

export function Counter() {
  const { count, inc } = useCounterStore();

  return (
    <div>
      <h2>Counter Store</h2>
      <h4>{count}</h4>
      <button onClick={inc}>One Up</button>
    </div>
  );
}
```

```tsx
// components/counter/index.ts
export * from "./counter";
```

```tsx
// components/counter/counter.test.tsx
import { act, render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

import { Counter } from "./counter";

describe("Counter", () => {
  test("should render with initial state of 1", async () => {
    renderCounter();

    expect(await screen.findByText(/^1$/)).toBeInTheDocument();
    expect(
      await screen.findByRole("button", { name: /one up/i })
    ).toBeInTheDocument();
  });

  test("should increase count by clicking a button", async () => {
    const user = userEvent.setup();

    renderCounter();

    expect(await screen.findByText(/^1$/)).toBeInTheDocument();

    await user.click(await screen.findByRole("button", { name: /one up/i }));

    expect(await screen.findByText(/^2$/)).toBeInTheDocument();
  });
});

const renderCounter = () => {
  return render(<Counter />);
};
```

```tsx
// components/counter-with-context/counter-with-context.tsx
import {
  CounterStoreProvider,
  useCounterStoreContext,
} from "../../contexts/use-counter-store-context";

const Counter = () => {
  const { count, inc } = useCounterStoreContext((state) => state);

  return (
    <div>
      <h2>Counter Store Context</h2>
      <h4>{count}</h4>
      <button onClick={inc}>One Up</button>
    </div>
  );
};

export const CounterWithContext = () => {
  return (
    <CounterStoreProvider>
      <Counter />
    </CounterStoreProvider>
  );
};
```

```ts
// components/counter-with-context/index.ts
export * from "./counter-with-context";
```

```tsx
// components/counter-with-context/counter-with-context.test.tsx
import { act, render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

import { CounterWithContext } from "./counter-with-context";

describe("CounterWithContext", () => {
  test("should render with initial state of 1", async () => {
    renderCounterWithContext();

    expect(await screen.findByText(/^1$/)).toBeInTheDocument();
    expect(
      await screen.findByRole("button", { name: /one up/i })
    ).toBeInTheDocument();
  });

  test("should increase count by clicking a button", async () => {
    const user = userEvent.setup();

    renderCounterWithContext();

    expect(await screen.findByText(/^1$/)).toBeInTheDocument();

    await user.click(await screen.findByRole("button", { name: /one up/i }));

    expect(await screen.findByText(/^2$/)).toBeInTheDocument();
  });
});

const renderCounterWithContext = () => {
  return render(<CounterWithContext />);
};
```

> **참고**: `globals` 설정이 활성화되어 있지 않다면, 각 테스트 파일 상단에 `import { describe, test, expect } from 'vitest'`를 추가해야 합니다.

## CodeSandbox 데모

- **Jest 데모**: [https://stackblitz.com/edit/jest-zustand](https://stackblitz.com/edit/jest-zustand)
- **Vitest 데모**: [https://stackblitz.com/edit/vitest-zustand](https://stackblitz.com/edit/vitest-zustand)

## Store 테스트하기

다음 예제들에서는 `useCounterStore`를 사용할 예정입니다.

> **참고**: 이 예제들은 모두 TypeScript로 작성되었습니다.

```ts
// shared/counter-store-creator.ts
import { type StateCreator } from "zustand";

export type CounterStore = {
  count: number;
  inc: () => void;
};

export const counterStoreCreator: StateCreator<CounterStore> = (set) => ({
  count: 1,
  inc: () => set((state) => ({ count: state.count + 1 })),
});
```

```ts
// stores/use-counter-store.ts
import { create } from "zustand";

import {
  type CounterStore,
  counterStoreCreator,
} from "../shared/counter-store-creator";

export const useCounterStore = create<CounterStore>()(counterStoreCreator);
```

```tsx
// contexts/use-counter-store-context.tsx
import { type ReactNode, createContext, useContext, useRef } from "react";
import { createStore } from "zustand";
import { useStoreWithEqualityFn } from "zustand/traditional";
import { shallow } from "zustand/shallow";

import {
  type CounterStore,
  counterStoreCreator,
} from "../shared/counter-store-creator";

export const createCounterStore = () => {
  return createStore<CounterStore>(counterStoreCreator);
};

export type CounterStoreApi = ReturnType<typeof createCounterStore>;

export const CounterStoreContext = createContext<CounterStoreApi | undefined>(
  undefined
);

export interface CounterStoreProviderProps {
  children: ReactNode;
}

export const CounterStoreProvider = ({
  children,
}: CounterStoreProviderProps) => {
  const counterStoreRef = useRef<CounterStoreApi>(null);
  if (!counterStoreRef.current) {
    counterStoreRef.current = createCounterStore();
  }

  return (
    <CounterStoreContext.Provider value={counterStoreRef.current}>
      {children}
    </CounterStoreContext.Provider>
  );
};

export type UseCounterStoreContextSelector<T> = (store: CounterStore) => T;

export const useCounterStoreContext = <T,>(
  selector: UseCounterStoreContextSelector<T>
): T => {
  const counterStoreContext = useContext(CounterStoreContext);

  if (counterStoreContext === undefined) {
    throw new Error(
      "useCounterStoreContext must be used within CounterStoreProvider"
    );
  }

  return useStoreWithEqualityFn(counterStoreContext, selector, shallow);
};
```

```tsx
// components/counter/counter.tsx
import { useCounterStore } from "../../stores/use-counter-store";

export function Counter() {
  const { count, inc } = useCounterStore();

  return (
    <div>
      <h2>Counter Store</h2>
      <h4>{count}</h4>
      <button onClick={inc}>One Up</button>
    </div>
  );
}
```

```ts
// components/counter/index.ts
export * from "./counter";
```

```tsx
// components/counter/counter.test.tsx
import { act, render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

import { Counter, useCounterStore } from "../../../stores/use-counter-store.ts";

describe("Counter", () => {
  test("should render with initial state of 1", async () => {
    renderCounter();
    expect(useCounterStore.getState().count).toBe(1);
  });

  test("should increase count by clicking a button", async () => {
    const user = userEvent.setup();
    renderCounter();
    expect(useCounterStore.getState().count).toBe(1);
    await user.click(await screen.findByRole("button", { name: /one up/i }));
    expect(useCounterStore.getState().count).toBe(2);
  });
});

const renderCounter = () => {
  return render(<Counter />);
};
```

```tsx
// components/counter-with-context/counter-with-context.tsx
import {
  CounterStoreProvider,
  useCounterStoreContext,
} from "../../contexts/use-counter-store-context";

const Counter = () => {
  const { count, inc } = useCounterStoreContext((state) => state);

  return (
    <div>
      <h2>Counter Store Context</h2>
      <h4>{count}</h4>
      <button onClick={inc}>One Up</button>
    </div>
  );
};

export const CounterWithContext = () => {
  return (
    <CounterStoreProvider>
      <Counter />
    </CounterStoreProvider>
  );
};
```

```ts
// components/counter-with-context/index.ts
export * from "./counter-with-context";
```

```tsx
// components/counter-with-context/counter-with-context.test.tsx
import { act, render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

import { CounterStoreContext } from "../../../contexts/use-counter-store-context";
import { counterStoreCreator } from "../../../shared/counter-store-creator";
import { CounterWithContext } from "./counter-with-context";

describe("CounterWithContext", () => {
  test("should render with initial state of 1", async () => {
    const counterStore = counterStoreCreator();
    renderCounterWithContext(counterStore);
    expect(counterStore.getState().count).toBe(1);
    expect(
      await screen.findByRole("button", { name: /one up/i })
    ).toBeInTheDocument();
  });

  test("should increase count by clicking a button", async () => {
    const user = userEvent.setup();
    const counterStore = counterStoreCreator();
    renderCounterWithContext(counterStore);
    expect(counterStore.getState().count).toBe(1);
    await user.click(await screen.findByRole("button", { name: /one up/i }));
    expect(counterStore.getState().count).toBe(2);
  });
});

const renderCounterWithContext = (store) => {
  return render(<CounterWithContext />, {
    wrapper: ({ children }) => (
      <CounterStoreContext.Provider value={store}>
        {children}
      </CounterStoreContext.Provider>
    ),
  });
};
```

## 참고 자료

- **React Testing Library**: [React Testing Library (RTL)](https://testing-library.com/docs/react-testing-library/intro)는 React 컴포넌트를 테스트하기 위한 매우 경량의 솔루션입니다. `react-dom` 및 `react-dom/test-utils` 위에 유틸리티 함수를 제공하며, 더 나은 테스트 관행을 장려합니다. 주요 원칙은 "사용자 관점에서 소프트웨어를 테스트할수록 더 높은 신뢰를 제공한다"입니다.

- **Native Testing Library**: [Native Testing Library (RNTL)](https://testing-library.com/docs/react-native-testing-library/intro)는 React Native 컴포넌트를 테스트하기 위한 매우 경량의 솔루션이며, React Testing Library와 유사하지만 `react-test-renderer`를 기반으로 구현됩니다.

- **구현 세부사항 테스트 회피**: [Kent C. Dodds의 블로그 글](https://kentcdodds.com/blog/testing-implementation-details)은 왜 구현 세부사항 테스트를 피해야 하는지에 대해 설명합니다.
