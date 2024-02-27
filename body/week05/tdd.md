# 1. TDD

## 학습 키워드

- TDD란
- Jest
- Describe - Context - It 패턴
- 단위테스트란

### ⚠️ TDD 에 대해서 주의할 점

테스트 코드를 작성한다고 해서 TDD 가 아닙니다.\
TDD Cycle 에 따라 테스트 코드를 먼저 작성하고, 구현하고, 리팩터링 하는 과정을 엄격하게 지켜서 개발을 진행해야 TDD 라고 할 수 있습니다.\
TDD 는 테스트 코드를 작성하는 것과 별개로 따로 연습이 필요하고 습관을 들여야하는 분야 입니다. 그리고 TDD 를 잘 하기 위해서는 테스트 코드 작성법 자체부터 공부를 하셔야 합니다.

## TDD (Test Driven Development)

💡 테스트 코드를 먼저 작성하는, 즉 구현보다 인터페이스와 스펙을 먼저 정의함으로써 개발을 진행하는 방식.\
여기서 말하는 인터페이스는 시그니처들의 모음으로, 시그니처는 각 함수 또는 메서드의 입력과 출력을 명시하는 것입니다.\
스펙은 코드 블록, 함수, 모듈의 동작과 기능에 대한 상세한 설명이나 규격을 나타냅니다.

### TDD Cycle

1. Red → 실패하는 테스트 코드를 작성. 인터페이스와 스펙에 집중합니다.
2. Green → 재빨리 테스트를 통과시킨다. 올바른 방법이 아니어도 괜찮습니다.
3. Refactor → 리팩터링을 통해 코드를 올바르게 만든다. TDD에서 가장 중요한 부분이지만, 간과될 때가 많습니다.

작은 단계를 찾고, 코드에서 피드백을 얻는 게 (어렵고) 중요합니다.\
2번이 어렵다면 1번으로 돌아가서 더 작고 쉬운 문제를 정의하고, 3번을 위해 의도를 드러내고 중복을 찾아 제거하는 연습을 해야 합니다.\
이 둘이 익숙하지 않으면 TDD를 하는 게 거의 불가능하고, 사실 이 둘이 어려우면 일반적인 개발 또는 클린 코드를 작성하는 것 또한 매우 힘듭니다.

## Jest

```bash
npx jest
```

```bash
npx jest --watchAll
```

테스트 케이스를 정의할 때 크게 두 가지 방법을 사용합니다:

1. test 함수로 개별 테스트를 나열하는 방식.(`App.test.ts`)
2. BDD 스타일로 주체-행위 중심으로 테스트를 조직화하는 방식.(`App.spec.ts`)

### Red

처음에는 test 함수로 개별 테스트를 써봅니다.

```jsx
test('add', () => {
  expect(add(1, 2)).toBe(3);
});
```

BDD 스타일로 테스트 대상과 행위를 명확히 드러냅니다.

```jsx
describe('add', () => {
  it('returns sum of two numbers', () => {
    expect(add(1, 2)).toBe(3);
  });
});
```

#### Describe - Context - It 패턴

다양한 경우를 고려해 봅니다.

```jsx
const context = describe;

describe('add', () => {
  context('with no argument', () => {
    it('returns zero', () => {
      expect(add()).toBe(0);
    });
  });

  context('with only one number', () => {
    it('returns the same number', () => {
      expect(add(1)).toBe(1);
    });
  });

  context('with two numbers', () => {
    it('returns sum of two numbers', () => {
      expect(add(1, 2)).toBe(3);
    });
  });

  context('with three numbers', () => {
    it('returns sum of three numbers', () => {
      expect(add(1, 2, 3)).toBe(6);
    });
  });
});
```

`context`는 주로 'when'이나 'with'으로 시작합니다.

### Green

최대한 빠르게 test 코드를 통과시킵니다.\
최대 인자가 3개이기 때문에 단순히 3개의 인자를 모두 더해줍니다.

```jsx
function add(...numbers: number[]): number {
  return (numbers[0] ?? 0) + (numbers[1] ?? 0) + (numbers[2] ?? 0);
}
```

### Refactor

조건문을 사용하여 나누어 봤더니 중복되는 부분이 보입니다.

```jsx
function add(...numbers: number[]): number {
  if (numbers.length === 0) {
    return 0;
  }

  if (numbers.length === 1) {
    return numbers[0];
  }

  if (numbers.length === 2) {
    return numbers[0] + numbers[1];
  }

  if (numbers.length === 3) {
    return numbers[0] + numbers[1] + numbers[2];
  }
}
```

`numbers.length`가 2일때와 `numbers.length`가 3일때 중복되는 부분 `numbers[0] + numbers[1];`이 있습니다.\

```jsx
function add(...numbers: number[]): number {
  if (numbers.length === 0) {
    return 0;
  }

  if (numbers.length === 1) {
    return numbers[0];
  }

  if (numbers.length === 2) {
    return numbers[0] + numbers[1];
  }

  if (numbers.length === 3) {
    return add(numbers[0] + numbers[1]) + numbers[numbers.length - 1];
  }
}
```

`add(numbers[0] + numbers[1])`재귀 함수에 `slice`를 사용해봅니다.\
`numbers`의 `length`를 하나씩 줄여가며 값을 구하기 때문에 가장 작은 0을 제외한 조건문은 불필요합니다.

```jsx
function add(...numbers: number[]): number {
  if (numbers.length === 0) {
    return 0;
  }

  return (
    add(...numbers.slice(0, numbers.length - 1)) + numbers[numbers.length - 1]
  );
}
```

재귀로 하는 것들을 `reduce`메서드로 대체하면 더 효율적으로 사용할 수 있습니다.

```jsx
function add(...numbers: number[]): number {
  if (numbers.length === 0) {
    return 0;
  }

  return numbers.reduce((acc, number) => acc + number);
}
```

### Jest 테스트 프레임워크 설정

Jest에서 TypeScript 사용하도록 `jest.config.js` 파일 작성

```jsx
module.exports = {
  // Jest가 테스트를 JavaScript DOM (jsdom) 환경에서 실행하도록 지정
  testEnvironment: 'jsdom',
  // Jest 환경 설정 이후에 실행되는 파일
  setupFilesAfterEnv: ['@testing-library/jest-dom/extend-expect'],
  // 파일 확장자가 .ts, .tsx, .js, .jsx인 파일들을 대상으로 변환을 수행
  transform: {
    '^.+\\.(t|j)sx?$': [
      // @swc/jest를 사용하여 변환
      '@swc/jest',
      {
        jsc: {
          parser: {
            // @swc/jest 변환기에게 TypeScript 문법을 사용하도록 설정
            syntax: 'typescript',
            // JSX 구문 파싱
            jsx: true,
            // 데코레이터(Decorator)도 파싱
            decorators: true,
          },
          // React 코드를 변환할 때 React 17 버전부터 도입된 JSX 변환(runtime: automatic)을 사용
          transform: {
            react: {
              runtime: 'automatic',
            },
          },
        },
      },
    ],
  },
  // est가 테스트를 찾을 때 무시할 파일 또는 디렉토리를 지정
  testPathIgnorePatterns: ['<rootDir>/node_modules/', '<rootDir>/dist/'],
};
```

## 단위테스트란

보통 함수나 별개의 React 컴포넌트 코드의 한 Unit 혹은 단위를 테스트하는 겁니다.\
Unit 다른 코드의 유닛과 상호 작용하는 것을 테스트하지 않습니다.\
Unit 테스트는 테스트를 최대한 격리시키고 함수나 컴포넌트를 테스트할 때 의존성을 표시합니다.

장점으로는 테스트가 코드의 한 Unit에 격리되어 있기 때문에 테스트가 실패하면 어디를 확인해야 하는지 정확하게 알 수 있습니다.

단점으로는 사용자가 소프트웨어와 상호 작용하는 방식과는 거리가 멀기 때문에 소프트웨어와 상호 작용하는 사용자가 실패하더라도 테스트를 통과할 수 있고 그 반대로 사용자가 소프트웨어와 상호 작용하는데 문제가 없어도 테스트에 실패할 수 있습니다.\
또한 리팩토링(Refactoring)으로 동작을 변경하지 않고 소프트웨어 작성 방식을 변경하는 것만으로 테스트에 실패할 수 있습니다.\
따라서 소프트웨어가 제대로 작동하면 테스트도 통과해야 하기 때문에 이런 점은 단점이 됩니다.

## 참고 자료

- [테스트 주도 개발](https://github.com/ahastudio/til/blob/main/agile/test-driven-development.md)
- [TDD FAQ](https://github.com/ahastudio/til/blob/main/blog/2016/12-03-tdd-faq.md)
- [Jest를 이용한 간단한 TDD 예제](https://github.com/ahastudio/til/blob/main/jest/20201204-simple-tdd-example.md)
- [Jest](https://jestjs.io/)
- [Given-When-Then](https://github.com/ahastudio/til/blob/main/blog/2018/12-08-given-when-then.md)
