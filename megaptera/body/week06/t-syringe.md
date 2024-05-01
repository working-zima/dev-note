# 2. TSyringe

## 학습 키워드

- TSyringe
- 의존성 주입(Dependency Injection)
- reflect-metadata
- singleton (싱글톤)

## TSyringe

TypeScript용 DI 도구(IoC Container). External Store를 관리하는데 활용할 수 있습니다.\
React 컴포넌트 입장에서는 “전역”처럼 여겨집니다.\
“Prop Drilling” 문제를 우아하게 해결할 수 있는 방법 중 하나입니다.\
(React로 한정하면 Context도 쓸 수 있습니다)

### 의존성 주입(Dependency Injection)

의존성 주입이란 외부에서 의존 객체를 생성하여 넘겨주는 것을 의미합니다.\
의존성을 외부에서 주입 받아 의존성을 없애고 객체간 결합도를 낮춥니다.

### reflect-metadata

#### reflect-metadata 의존성 설치

```bash
npm i tsyringe reflect-metadata
```

#### reflect-metadata 적용

모든 프로그램이 시작하는 `src/main.tsx` 파일과 `src/setupTests.ts` 파일에서 reflect-metadata 임포트.
reflect-metadata는 TypeScript에서는 런타임에 타입 정보를 사용하기 위해 사용하는 polyfill.

```tsx
import 'reflect-metadata';
```

```javascript
// jest.config.js

setupFilesAfterEnv: [
  '@testing-library/jest-dom/extend-expect',
  '<rootDir>/src/setupTests.ts', // 이 부분 없다면 추가
],
```

### 데코레이터 함수

```tsx
// @ 기호는 타입스크립트에게 데코레이터임을 알려줍니다.

@expression
```

데코레이터는 일종의 함수 입니다.\
함수를 일급 시민으로서의 기능을 지원하는 모든 언어는 데코레이터를 구현할 수 있습니다.\
클래스 선언, 메서드, 접근자, 프로퍼티 또는 매개 변수에 `@expression`을 장식해주면, 런타임에서 데코레이터 함수를 호출합니다.\
데코레이터 아래의 메소드나 클래스 인스턴스가 만들어지는 런타임에 실행이되며 매번 실행되는 것은 아닙니다.

#### 데코레이터 팩토리

데코레이터가 런타임에 호출할 표현식을 반환하는 함수입니다.

```tsx
function color(value: string) { // 데코레이터 팩토리
    return function (target) { // 데코레이터
        // 'target'과 'value' 변수를 가지고 무언가를 수행합니다.
  };
}
```

#### 여러 데코레이터를 사용할 때 수행 단계

1. 각 데코레이터의 표현은 위에서 아래로 평가됩니다.
2. 그런 다음 결과는 아래에서 위로 함수로 호출됩니다.

```tsx
// @experimentalDecorators
function first() {
  console.log("first(): factory evaluated"); // 1
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    console.log("first(): called"); // 4
  };
}

function second() {
  console.log("second(): factory evaluated"); // 2
  return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
    console.log("second(): called"); // 3
  };
}

class ExampleClass {
  @first()
  @second()
  method() {}
}
```

콘솔 출력입니다.

```bash
[LOG]: "first(): factory evaluated"
[LOG]: "second(): factory evaluated"
[LOG]: "second(): called"
[LOG]: "first(): called"
```

### singleton

`TSyringe`에서 제공하는 클래스 데코레이터 팩토리로, 해당 클래스를 전역 컨테이너 내에서 싱글톤으로 등록합니다.
싱글톤으로 등록되면, 여러 곳에서 동일한 인스턴스를 공유하고, 외부에서 인스턴스를 직접 생성하지 못하게 클래스의 생성자를 막습니다.

### container

IoC 컨테이너는 프로그램에서 필요한 부품들을 관리해주는데 도움이 되는 도구입니다.\
IoC 컨테이너는 토큰이라는 것을 주고, 그에 따른 인스턴스나 값이나 객체를 돌려줍니다.
즉, 부품들 간의 의존성을 쉽게 해결할 수 있도록 도와주는 도구입니다.

#### container.resolve()

의존성 주입 컨테이너에서 사용되는 메서드로, 특정한 토큰에 해당하는 객체를 가져오는 데 사용됩니다.\
주어진 토큰에 대한 인스턴스를 생성하거나 이미 생성된 인스턴스를 반환합니다.\
코드에서 직접적인 객체 생성과 의존성 해결을 피할 수 있으며, 유연성과 테스트 용이성을 향상시킬 수 있습니다.

#### 사용 예시

```tsx
import {singleton} from "tsyringe";

// Foo 클래스를 singleton으로 등록
@singleton()
class Foo {
  constructor() {}
}
```

```tsx
// some other file
import "reflect-metadata";
import {container} from "tsyringe";
import {Foo} from "./foo";

// Foo에 해당하는 인스턴스 생성 또는 반환
const instance = container.resolve(Foo);
```

### 싱글톤으로 관리할 CounterStore 클래스를 준비

```tsx
import { singleton } from 'tsyringe';

@singleton()
class CounterStore {
  // …(중략)...
}
```

만약 `@singleton()`에 오류가 있다면 `tscocnfig.json` 설정 확인('@' 데코레이터 문제일 경우)

```json
// tscocnfig.json

{
  "compilerOptions": {
    "experimentalDecorators": true,
    // 나머지 옵션들...
  },
  // 다른 설정들...
}
```

### 싱글톤 CounterStore 객체를 사용

```tsx
import { container } from 'tsyringe';

const counterStore = container.resolve(CounterStore);
```

테스트에서 TSyringe에서 관리하는 객체를 초기화할 수 있다.

```tsx
container.clearInstances();
```

## 상태 변경 알림

`Store`는 어떤 식으로든 `action`을 처리하고, 상태가 바뀌면 연결된 컴포넌트를 `forceUpdate`한다.

### TSyringe 사용 예시

![screen](./img/screen.png)

#### App.tsx

```tsx
import CountControl from './components/CountControl';
import Counter from './components/Counter';

function App() {
  return (
    <div>
      <p>Hello, world!</p>
      <Counter />
      <Counter />
      <CountControl />
    </div>
  );
}

export default App;
```

#### CounterStore.ts

TSyringe의 store으로 `count`를 관리

```tsx
/* eslint-disable no-shadow */
import { singleton } from 'tsyringe';

type Listener = () => void;

@singleton()
class CounterStore {
  count = 0;

  // 변수 listeners는 각 컴포넌트의 forceUpdate를 담을 set 객체
  listeners = new Set<Listener>();

  // 컴포넌트 각각의 forceUpdate를 호출
  publish() {
    this.listeners.forEach((listener) => {
      listener();
    });
  }

  // 변수 listeners에 특정 컴포넌트의 forceUpdate를 set 객체에 추가
  addListener(listener: listener) {
    this.listeners.add(listener);
  }

  // 변수 listeners에 특정 컴포넌트의 forceUpdate를 set 객체에서 제거
  removeListener(listener: listener) {
    this.listeners.delete(listener);
  }
}

export default CounterStore;
```

#### Counter.tsx

UI를 담당하는 컴포넌트

```tsx

import { container } from 'tsyringe';
import { useEffect } from 'react';

import CounterStore from '../stores/CounterStore';
import useForceUpdate from '../hooks/useForceUpdate';

function Counter() {
  // TSyringe의 store
  const store = container.resolve(CounterStore);

  // 강제로 리랜더링하는 함수
  const forceUpdate = useForceUpdate();

  // Counter 컴포넌트 마다 각자의 forceUpdate를 store에 저장
  useEffect(() => {
    store.addListener(forceUpdate);

    return () => store.removeListener(forceUpdate);
  }, [store, forceUpdate]);

  // store의 count를 화면에 출력
  return (
    <div>
      <p>
        Count:
        {' '}
        {store.count}
      </p>
    </div>
  );
}

export default Counter;
```

#### CountControl.tsx

Business를 담당하는 컴포넌트

```tsx
import { container } from 'tsyringe';

import CounterStore from '../stores/CounterStore';

function CountControl() {
  const store = container.resolve(CounterStore);

  // 버튼을 클릭하면 store의 count에 1을 더함
  const handleClickIncrease = () => {
    store.count += 1;
    // 컴포넌트 각각의 forceUpdate를 호출
    store.publish();
  };

  // 버튼을 클릭하면 store의 count에 1을 뺌
  const handleClickDecrease = () => {
    store.count -= 1;
    // 컴포넌트 각각의 forceUpdate를 호출
    store.publish();
  };

  return (
    <div>
      <button type="button" onClick={handleClickIncrease}>
        Increase
      </button>
      <button type="button" onClick={handleClickDecrease}>
        Decrease
      </button>
    </div>
  );
}

export default CountControl;
```

#### useForceUpdate.ts

호출될 시 리렌더링

```tsx
import { useCallback, useState } from 'react';

export default function useForceUpdate() {
  const [, setState] = useState({});

  // state를 변경시켜 리랜더링을 발생
  return useCallback(() => setState({}), []);
}
```

### test 코드 작성시 문제

'increase' 버튼을 한 번 눌렀을 때와 두 번 눌렀을 때를 테스트 했습니다.\
한 번 눌렀을 때의 테스트는 통과했지만, 두 번 눌렀을 때의 테스트는 실패했습니다.\
이유는 첫 번째 테스트에서 한 번 누른 것이 두 번째 테스트에 영향을 주어 결국 세번 누른게 되었기 때문입니다.\
각각의 테스트 코드는 독립적이여야 하지만 전역 변수를 사용하면서 독립적이지 않게 되었습니다.

```tsx
// App.test.tsx

import { fireEvent, render, screen } from '@testing-library/react';

import App from './App';

const context = describe;

test('App', () => {
  render(<App />);
});

describe('App', () => {
  // 통과
  context('when press increase button once', () => {
    it('counter', () => {
      render(<App />);

      fireEvent.click(screen.getByText('Increase'));

      expect(screen.getAllByText('Count: 1')).toHaveLength(2);
    });
  });

  // 실패
  context('when press increase button twice', () => {
    it('counter', () => {
      render(<App />);

      fireEvent.click(screen.getByText('Increase'));
      fireEvent.click(screen.getByText('Increase'));

      // 'Count: 3'일 때 통과
      expect(screen.getAllByText('Count: 2')).toHaveLength(2);
    });
  });
});
```

#### 문제 해결

`beforeEach`와 `container.clearInstances();`을 사용합니다.\
각각의 테스트 전에 전역 변수를 초기화하여 테스트 코드를 독립적으로 바꿉니다.

```tsx
// App.test.tsx

import { fireEvent, render, screen } from '@testing-library/react';
import { container } from 'tsyringe';

import App from './App';

const context = describe;

test('App', () => {
  render(<App />);
});

describe('App', () => {
  // 전역 변수를 원래대로 초기화
  beforeEach(() => {
    container.clearInstances();
  });

  context('when press increase button once', () => {
    it('counter', () => {
      render(<App />);

      fireEvent.click(screen.getByText('Increase'));

      expect(screen.getAllByText('Count: 1')).toHaveLength(2);
    });
  });

  context('when press increase button twice', () => {
    it('counter', () => {
      render(<App />);

      fireEvent.click(screen.getByText('Increase'));
      fireEvent.click(screen.getByText('Increase'));

      expect(screen.getAllByText('Count: 2')).toHaveLength(2);
    });
  });
});
```

### 캡슐화 & custom hook

기존 코드를 리팩토링할 수 있습니다.

#### Counter.tsx 변경

UI 부분만 남기고 나머지 로직을 custom hook으로 뺐습니다.

```tsx
import useCounterStore from '../hooks/useCounterStore';

function Counter() {
  const store = useCounterStore();

  return (
    <div>
      <p>
        Count:
        {' '}
        {store.count}
      </p>
    </div>
  );
}

export default Counter;
```

#### useCounterStore.tsx 생성

```tsx
import { container } from 'tsyringe';
import { useEffect } from 'react';

import CounterStore from '../stores/CounterStore';
import useForceUpdate from './useForceUpdate';

function useCounterStore() {
  const store = container.resolve(CounterStore);

  const forceUpdate = useForceUpdate();

  useEffect(() => {
    store.addListener(forceUpdate);

    return () => store.removeListener(forceUpdate);
  }, [store, forceUpdate]);

  return store;
}

export default useCounterStore;
```

#### CountControl.tsx 변경

`handleClickIncrease`와 `handleClickDecrease`의 내부 로직을 캡슐화 하였습니다.

```tsx
import { container } from 'tsyringe';

import CounterStore from '../stores/CounterStore';

function CountControl() {
  const store = container.resolve(CounterStore);

  const handleClickIncrease = () => {
    store.increase();
  };

  const handleClickDecrease = () => {
    store.decrease();
  };

  return (
    <div>
      <button type="button" onClick={handleClickIncrease}>
        Increase
      </button>
      <button type="button" onClick={handleClickDecrease}>
        Decrease
      </button>
    </div>
  );
}

export default CountControl;
```

#### CounterStore.ts 변경

```tsx
/* eslint-disable no-shadow */
import { singleton } from 'tsyringe';

type listener = () => void;

@singleton()
class CounterStore {
  count = 0;

  listeners = new Set<listener>();

  increase() {
    this.count += 1;
    this.publish();
  }

  decrease() {
    this.count -= 1;
    this.publish();
  }

  publish() {
    this.listeners.forEach((listener) => {
      listener();
    });
  }

  addListener(listener: listener) {
    this.listeners.add(listener);
  }

  removeListener(listener: listener) {
    this.listeners.delete(listener);
  }
}

export default CounterStore;
```

컴포넌트는 해당 `Store`에서 상태를 얻어서 UI를 업데이트하게 되는데, 선언형 UI가 얼마나 편한지 절실히 느낄 수 있습니다.

## 참고 자료

- [TSyringe](https://github.com/microsoft/tsyringe)
- [reflect-metadata](https://github.com/rbuckton/reflect-metadata)
- [The problem with passing props](https://beta.reactjs.org/learn/passing-data-deeply-with-context#the-problem-with-passing-props)
- [Decorators](https://www.typescriptlang.org/ko/docs/handbook/decorators.html)
