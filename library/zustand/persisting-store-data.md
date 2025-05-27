# Persisting store data

React 앱에서 어떤 데이터를 useState, useReducer, Zustand 등으로 관리하면, 이 상태들은 브라우저 메모리에만 잠깐 저장되기 때문에 페이지를 새로고침하거나 닫았다가 다시 열면 그 데이터는 사라집니다.\
persist 미들웨어를 사용하면 Zustand 상태를 저장소(`localStorage`, `AsyncStorage`, `IndexedDB` 등)에 저장하여 상태 데이터를 지속(`persist`)시킬 수 있습니다.\
이렇게 하면 사용자가 페이지를 새로고침해도 이전 상태가 복원됩니다.

이 미들웨어는 `localStorage`처럼 동기적인 저장소와 `AsyncStorage`처럼 비동기적인 저장소 모두를 지원합니다.\
단, 비동기 저장소는 비용이 따릅니다.\
이에 대해서는 아래 "Hydration과 비동기 저장소" 섹션에서 자세히 다룹니다.

## 예제

- `persist(...)`로 미들웨어 감싸기
- `name`: 저장소에 저장될 키 이름. 고유해야 함.
- `storage`: 기본은 localStorage인데, 예제에서는 sessionStorage를 사용

```ts
import { create } from "zustand";
import { persist, createJSONStorage } from "zustand/middleware";

type BearStore = {
  bears: number;
  addABear: () => void;
};

export const useBearStore = create<BearStore>()(
  persist(
    (set, get) => ({
      bears: 0,
      addABear: () => set({ bears: get().bears + 1 }),
    }),
    {
      name: "food-storage", // 저장소에서 사용할 고유한 키
      storage: createJSONStorage(() => sessionStorage), // (선택사항) 기본값은 localStorage
    }
  )
);
```

## persist 옵션

### name (필수)

`name`은 상태를 저장소에 저장할 때 키로 사용됩니다.\
반드시 고유해야 합니다.

### storage

상태를 어디에 저장할지를 정하는 것입니다.\
`localStorage`, `sessionStorage`, `IndexedDB` 등 선택 가능합니다.

#### storage: `() => StateStorage`

zustand/middleware에서 `StateStorage`를 import하여 사용할 수 있습니다.

```ts
import { StateStorage } from "zustand/middleware";
```

#### 기본값: `createJSONStorage(() => localStorage)`

사용할 저장소를 지정할 수 있습니다.\
`createJSONStorage` 헬퍼를 이용해 `StateStorage` 인터페이스를 따르는 저장소 객체를 쉽게 생성할 수 있습니다.

#### storage 예시

```ts
import { persist, createJSONStorage } from "zustand/middleware";

export const useBoundStore = create(
  persist(
    (set, get) => ({
      // ...
    }),
    {
      // ...
      storage: createJSONStorage(() => AsyncStorage),
    }
  )
);
```

### partialize

어떤 값만 저장할지 정할 수 있습니다.\
전체 상태가 아니라, 일부 상태만 저장하고 싶을 때 사용합니다.

#### partialize: `(state: Object) => Object`

#### 기본값: `(state) => state`

저장할 상태 필드를 선택할 수 있습니다.

```ts
// state의 foo만 저장
partialize: (state) => ({ bears: state.bears });
```

특정 필드를 제외하려면:

```ts
// foo는 저장하지 말고 나머지만 저장
partialize: (state) =>
  Object.fromEntries(
    Object.entries(state).filter(([key]) => !["foo"].includes(key))
  );
```

### onRehydrateStorage

스토리지에 hydrate(복원) 될 때 호출되는 리스너를 등록할 수 있습니다.

```ts
onRehydrateStorage: (state: Object) => ((state?: Object, error?: Error) => void) | void
```

#### onRehydrateStorage 예시

스토어가 복원되기 시작할 때와 끝났을 때 원하는 행동을 넣을 수 있습니다.

```ts
onRehydrateStorage: (state) => {
  console.log("hydration starts");

  return (state, error) => {
    if (error) {
      console.log("an error happened during hydration", error);
    } else {
      console.log("hydration finished");
    }
  };
};
```

#### hydration 이란

저장소에서 이전 상태를 꺼내서 스토어에 넣는 과정입니다.\
동기 저장소(localStorage)는 바로 되고, 비동기 저장소(IndexedDB 등)는 나중에 됩니다.

→ 비동기 저장소를 쓰면 첫 렌더 시 값이 비어 있을 수 있습니다.
이걸 방지하려면 hydrate 여부를 확인해야 합니다.

```ts
const hasHydrated = useBoundStore((state) => state._hasHydrated);
```

Next.js에서 쓸 땐 주의해야 합니다.

> Next.js는 서버에서 먼저 렌더링 → 클라이언트에서 다시 렌더링 → hydrate 안 된 상태로 먼저 화면이 그려지면 에러

이렇게 커스텀 훅으로 상태가 준비된 뒤 렌더링하게 만들 수 있습니다.

```ts
const bears = useStore(useBearStore, (state) => state.bears);
```

### version

`version` + `migrate`는 저장된 데이터 형식이 바뀌었을 때 사용하는 버전관리 도구입니다.

```ts
version: number;
```

#### 기본값: `0`

저장된 상태 구조에 변경이 필요한 경우 새로운 버전 번호를 지정할 수 있습니다.\
저장된 버전과 코드의 버전이 다르면 기본적으로 저장된 값은 사용되지 않습니다.\
이전 데이터를 계속 유지하고 싶다면 `migrate` 함수를 함께 사용합니다.

### migrate

```ts
migrate: (persistedState: Object, version: number) => Object | Promise<Object>;
```

#### 기본값: `(persistedState) => persistedState`

저장된 상태를 새로운 버전에 맞게 마이그레이션할 수 있도록 하는 함수입니다.\
예를 들어 필드명을 변경하는 경우:

```ts
migrate: (persistedState, version) => {
  if (version === 0) {
    persistedState.newField = persistedState.oldField;
    delete persistedState.oldField;
  }

  return persistedState;
};
```

구조가 바뀌면 저장된 데이터와 안 맞을 수 있습니다.\
이럴 땐 `version`을 올리고 `migrate`로 데이터를 변환시킵니다.

```ts
version: 1,
migrate: (persistedState, version) => {
  if (version === 0) {
    persistedState.newField = persistedState.oldField;
    delete persistedState.oldField;
  }
  return persistedState;
}
```

### merge

복원한 값과 현재 상태를 어떻게 합칠지 설정합니다.

```ts
merge: (persistedState: Object, currentState: Object) => Object;
```

기본값: `(persistedState, currentState) => ({ ...currentState, ...persistedState })`

기본은 얕은 병합(shallow merge)이지만, 깊은 병합(deep merge)을 원할 경우 직접 함수를 정의할 수 있습니다.

#### merge 예

복원할 때 단순히 ...으로 병합하지 말고, 깊은 병합(deep merge)을 하려면

```ts
merge: (persistedState, currentState) =>
  deepMerge(currentState, persistedState);
```

### skipHydration

자동 복원 하지 않고 수동으로 할 때 사용합니다.

기본적으로 상태는 앱 시작 시 자동으로 복원(hydrate) 됩니다.
하지만 서버사이드 렌더링을 고려할 땐 직접 제어할 수 있어야 합니다..

```ts
skipHydration: boolean | undefined;
```

기본값: `undefined`

기본적으로 상태는 초기화 시 자동으로 hydrate 됩니다.\
이를 직접 제어하고 싶다면 `skipHydration: true`를 설정한 후 수동으로 `rehydrate()`를 호출하면 됩니다.

```ts
useEffect(() => {
  useBoundStore.persist.rehydrate();
}, []);
```

## Persist API

Zustand Persist API는 컴포넌트 내부 또는 외부에서 `persist` 미들웨어와 상호작용할 수 있는 다양한 메서드를 제공합니다.

### getOptions

```ts
type: () => Partial<PersistOptions>;
```

현재 설정된 `persist` 미들웨어 옵션을 반환합니다.

#### getOptions 예

```ts
useBoundStore.persist.getOptions().name;
```

### setOptions

```ts
type: (newOptions: Partial<PersistOptions>) => void
```

현재 옵션과 병합할 새 옵션을 설정할 수 있습니다.

#### setOptions 예

```ts
useBoundStore.persist.setOptions({
  name: "new-name",
});

useBoundStore.persist.setOptions({
  storage: createJSONStorage(() => sessionStorage),
});
```

### clearStorage

```ts
type: () => void
```

`name`으로 설정된 키 아래 저장된 모든 항목을 삭제합니다.

```ts
useBoundStore.persist.clearStorage();
```

### rehydrate

```ts
type: () => Promise<void>;
```

스토어를 수동으로 hydrate(복원)할 수 있습니다.

```ts
await useBoundStore.persist.rehydrate();
```

### hasHydrated

```ts
type: () => boolean;
```

스토어가 hydrate 되었는지 확인하는 비반응형(non-reactive) getter입니다.

#### hasHydrated 예

```ts
useBoundStore.persist.hasHydrated();
```

### onHydrate

```ts
type: (listener: (state) => void) => () => void
```

hydrate가 시작될 때 실행할 콜백을 등록합니다. 반환값은 구독 해제 함수입니다.

#### onHydrate 예

```ts
const unsub = useBoundStore.persist.onHydrate((state) => {
  console.log("hydration starts");
});

// 해제:
unsub();
```

### onFinishHydration

```ts
type: (listener: (state) => void) => () => void
```

hydrate가 완료되었을 때 실행할 콜백을 등록합니다. 반환값은 구독 해제 함수입니다.

```ts
const unsub = useBoundStore.persist.onFinishHydration((state) => {
  console.log("hydration finished");
});

// 해제:
unsub();
```

### createJSONStorage

```ts
type: (getStorage: () => StateStorage, options?: JsonStorageOptions) =>
  StateStorage;
```

`StateStorage`에 맞게 직렬화/역직렬화를 지원하는 저장소 객체를 생성합니다.

#### createJSONStorage 예

```ts
const storage = createJSONStorage(() => sessionStorage, {
  reviver: (key, value) => {
    if (value && value.type === "date") {
      return new Date(value);
    }
    return value;
  },
  replacer: (key, value) => {
    if (key === "someDate") return { type: "date", value };
    return value;
  },
});
```

## Hydration과 비동기 저장소

### Hydration

간단히 말해, hydration은 저장소로부터 저장된 상태를 불러와서 현재 Zustand 상태와 병합하는 과정입니다.

Zustand의 `persist` 미들웨어는 두 가지 종류의 hydration을 수행합니다:

1. 동기적 Hydration
   예: `localStorage` 사용 시
   → Zustand 스토어가 생성될 때 즉시 hydrate 됩니다.

2. 비동기적 Hydration
   예: `AsyncStorage` 사용 시
   → Zustand 스토어가 생성된 이후에 hydrate 됩니다. (마이크로태스크에서 처리됨)

비동기 hydration은 예상치 못한 동작을 유발할 수 있습니다.

예를 들어 React 앱에서 Zustand 스토어를 사용하는 경우, 초기 렌더 시 스토어가 hydrate되지 않았기 때문에 기본값을 사용하게 됩니다.

- 이로 인해 예를 들어 사용자가 로그인 상태가 아님으로 잘못 판단될 수 있습니다.
- 실제로는 로그인 상태인데, 상태가 복원되기 전에 렌더가 이뤄졌기 때문입니다.

앱이 로딩 시점에 저장된 상태(예: 로그인 상태)에 의존한다면, hydrate 완료 여부를 기다리는 로직을 구현해야 합니다.

### hydrate 여부 확인 방법

예를 들어, 다음과 같은 코드로 구현 가능합니다.

```ts
const useBoundStore = create(
  persist(
    (set, get) => ({
      _hasHydrated: false,
      setHasHydrated: (state: boolean) => set({ _hasHydrated: state }),
    }),
    {
      onRehydrateStorage: () => {
        return () => useBoundStore.getState().setHasHydrated(true);
      },
    }
  )
);
```

이렇게 하면 React에서 `hasHydrated`를 기준으로 렌더 조건을 줄 수 있습니다.

```tsx
export default function App() {
  const hasHydrated = useBoundStore((state) => state._hasHydrated);

  if (!hasHydrated) {
    return <p>Loading...</p>;
  }

  return <MainApp />;
}
```

## Next.js에서의 사용

Next.js는 서버 사이드 렌더링(SSR) 을 사용하기 때문에, 서버에서 렌더링한 HTML과 클라이언트에서 초기 렌더링되는 HTML이 달라지면 Hydration 오류가 발생합니다.

주로 발생하는 오류:

- `Text content does not match server-rendered HTML`
- `Hydration failed because the initial UI does not match what was rendered on the server`

### 해결 방법

브라우저에서 상태가 복원될 때까지 렌더링을 지연시켜야 합니다.\
이를 위해 아래와 같이 커스텀 훅을 만들어 사용합니다.

```ts
// useStore.ts
import { useState, useEffect } from "react";

const useStore = <T, F>(
  store: (callback: (state: T) => unknown) => unknown,
  callback: (state: T) => F
) => {
  const result = store(callback) as F;
  const [data, setData] = useState<F>();

  useEffect(() => {
    setData(result);
  }, [result]);

  return data;
};

export default useStore;
```

페이지에서 사용할 때는 이렇게

```tsx
// useBearStore.ts
export const useBearStore = create(
  persist(
    (set, get) => ({
      bears: 0,
      addABear: () => set({ bears: get().bears + 1 }),
    }),
    {
      name: "food-storage",
    }
  )
);

// yourComponent.tsx
import useStore from "./useStore";
import { useBearStore } from "./stores/useBearStore";

const bears = useStore(useBearStore, (state) => state.bears);
```

## FAQ 및 고급 사용법

### 1. onRehydrateStorage를 사용해 상태 내에서 직접 확인 변수 설정

```ts
const useBoundStore = create(
  persist(
    (set, get) => ({
      _hasHydrated: false,
      setHasHydrated: (state: boolean) => set({ _hasHydrated: state }),
    }),
    {
      onRehydrateStorage: () => {
        return () => useBoundStore.getState().setHasHydrated(true);
      },
    }
  )
);
```

React 컴포넌트에서 확인

```tsx
export default function App() {
  const hasHydrated = useBoundStore((state) => state._hasHydrated);

  if (!hasHydrated) return <p>Loading...</p>;

  return <MainComponent />;
}
```

### 2. 커스텀 useHydration 훅 만들기

```ts
const useHydration = () => {
  const [hydrated, setHydrated] = useState(false);

  useEffect(() => {
    const unsubHydrate = useBoundStore.persist.onHydrate(() =>
      setHydrated(false)
    );
    const unsubFinish = useBoundStore.persist.onFinishHydration(() =>
      setHydrated(true)
    );

    setHydrated(useBoundStore.persist.hasHydrated());

    return () => {
      unsubHydrate();
      unsubFinish();
    };
  }, []);

  return hydrated;
};
```

### 커스텀 저장소를 사용하려면 어떻게 해야 하나요?

`getItem`, `setItem`, `removeItem` 메서드를 가진 객체를 만들면 됩니다.

예: `idb-keyval`을 활용한 IndexedDB 저장소

```ts
import { get, set, del } from "idb-keyval";
import { create } from "zustand";
import { persist, createJSONStorage, StateStorage } from "zustand/middleware";

const customStorage: StateStorage = {
  getItem: async (name) => {
    console.log(`${name} has been retrieved`);
    return (await get(name)) || null;
  },
  setItem: async (name, value) => {
    console.log(`${name} saved with value:`, value);
    await set(name, value);
  },
  removeItem: async (name) => {
    console.log(`${name} has been deleted`);
    await del(name);
  },
};

export const useBoundStore = create(
  persist(
    (set, get) => ({
      bears: 0,
      addABear: () => set({ bears: get().bears + 1 }),
    }),
    {
      name: "food-storage",
      storage: createJSONStorage(() => customStorage),
    }
  )
);
```

### Map, Set, Date 등 JSON.stringify() 불가능한 타입

`superjson` 같은 라이브러리를 써서 해결할 수 있습니다.

```ts
import superjson from "superjson";
import { PersistStorage } from "zustand/middleware";

interface BearState {
  bear: Map<string, string>;
  fish: Set<string>;
  time: Date;
  query: RegExp;
}

const storage: PersistStorage<BearState> = {
  getItem: (name) => {
    const str = localStorage.getItem(name);
    if (!str) return null;
    return superjson.parse(str);
  },
  setItem: (name, value) => {
    localStorage.setItem(name, superjson.stringify(value));
  },
  removeItem: (name) => {
    localStorage.removeItem(name);
  },
};
```

이렇게 하면 Map, Set, Date 등의 데이터도 안전하게 저장하고 복원할 수 있습니다.

### 스토리지 이벤트(storage 이벤트)로 수동 rehydrate 하고 싶다면?

브라우저 탭 간 동기화를 위해 스토리지 변경 시 자동으로 rehydrate 하려면 아래처럼 구현할 수 있습니다.

```ts
type StoreWithPersist = Mutate<StoreApi<State>, [["zustand/persist", unknown]]>

export const withStorageDOMEvents = (store: StoreWithPersist) => {
  const storageEventCallback = (e: StorageEvent) => {
    if (e.key === store.persist.getOptions().name && e.newValue) {
      store.persist.rehydrate()
    }
  }

  window.addEventListener('storage', storageEventCallback)

  return () => {
    window.removeEventListener('storage', storageEventCallback)
  }
}

const useBoundStore = create(persist(...))
withStorageDOMEvents(useBoundStore)
```

### TypeScript에서는 어떻게 쓰나요

기본적으로 아래처럼 제네릭을 사용하면 됩니다.

```ts
interface MyState {
  bears: number;
  addABear: () => void;
}

export const useBearStore = create<MyState>()(
  persist(
    (set, get) => ({
      bears: 0,
      addABear: () => set({ bears: get().bears + 1 }),
    }),
    {
      name: "food-storage",
      storage: createJSONStorage(() => sessionStorage),
      partialize: (state) => ({ bears: state.bears }),
    }
  )
);
```

### Map이나 Set을 어떻게 저장하나요

Map을 예시로 설명하자면, 저장 시 배열로 변환하고, 복원 시 다시 Map으로 복원하면 됩니다.

```ts
storage: {
  getItem: (name) => {
    const str = localStorage.getItem(name)
    if (!str) return null
    const existingValue = JSON.parse(str)
    return {
      ...existingValue,
      state: {
        ...existingValue.state,
        transactions: new Map(existingValue.state.transactions),
      },
    }
  },
  setItem: (name, newValue) => {
    const str = JSON.stringify({
      ...newValue,
      state: {
        ...newValue.state,
        transactions: Array.from(newValue.state.transactions.entries()),
      },
    })
    localStorage.setItem(name, str)
  },
  removeItem: (name) => localStorage.removeItem(name),
}
```
