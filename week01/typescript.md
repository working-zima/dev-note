# 2. TypeScript

간단히 REPL을 쓰고 싶다면 ts-node를 실행하면 됩니다.

```bash
npx ts-node
```

## 타입 지정

```typescript
let name: string;
let age: number;

name = '홍길동';
age = 13;

// 안되는 유형
name = 13;
age = '홍길동';

// 객체 타입
let human: {
  name: string;
  age: number;
};

human = { name: '홍길동', age: 13 };
```

배열은 타입 뒤에 대괄호를 붙여주면 됩니다.

```typescript
let numbers: number[];

numbers = [1, 2, 3];
```

배열보다 깐깐하게 타입을 관리하고 싶다면 `Tuple`을 쓴다.

```typescript
// any는 어떠한 타입도 가능하기 때문에 타입스크립트를 사용하는 의미가 퇴색
let anythings: any[];

anythings = ['hp', 256];

// 튜플은 길이와 순서, 타입이 고정되어 있는 배열
let pair: [string, number];

pair = ['hp', 256];
```

정해진 값으로 타입을 지정할 수도 있다. 이런 타입은 `Union`에서 유용하게 쓰입니다.

```typescript
let category: 'food';

category = 'food';
```

## 타입 추론

타입을 지정하지 않아도 타입 스크립트는 타입 추론을 통해 변수 선언에 어떤 타입 값이 할당되었는지 파악합니다.

```typescript
const name = '홍길동';
```

## 타입 별칭과 인터페이스

복잡한 오브젝트의 타입을 재사용하기 위해 타입을 정의할 수 있습니다.  
`interface`가 가지는 대부분의 기능은 `type`에서도 동일하게 사용 가능합니다.  
이 둘의 가장 핵심적인 차이는, 타입은 새 프로퍼티를 추가하도록 개방될 수 없는 반면, 인터페이스의 경우 항상 확장될 수 있다는 점입니다.

```typescript
// type
type Human = {
  name: string;
  age: number;
};

let boy: Human;

boy = { name: '홍길동', age: 13 };

// interface
interface Person {
  name: string;
  age: number;
}

let girl: Person;

girl = { name: '홍길동', age: 13 };

// 함수
function add(x: number, y: number): number {
  return x + y;
}

add(1, 2);

// 잘못된 경우
add('Hello', 'World');

function sub(x: number, y: number): string {
  return x - y;
}
```

## Union Type

여러 타입 중 하나입니다.  
예를 들어 `boolean`은 `true | false`라고 볼 수 있습니다.

```typescript
type bool = true | false;

let flag: bool;

flag = true;

flag = false;

// error
flag = 3;
```

매개변수를 제한하거나 할 때 매우 유용하게 쓸 수 있습니다.

```typescript
type Category = 'food' | 'toy' | 'bag';

function fetchProducts({ category }: { category: Category }) {
  console.log(`Fetch ${category}`);
}
```

레거시 환경 또는 코드 때문에 쓰지 않을 수 없습니다.  
`ReactNode` 같은 게 대표적입니다.

```typescript
export type ReactNode =
  | React$Element<any>
  | ReactPortal
  | ReactText
  | ReactFragment
  | ReactProvider<any>
  | ReactConsumer<any>;
```

그냥 `undefined`를 쓸 일은 없고, 함수 매개변수(parameters)에서 사용되곤 합니다.

```typescript
let target: string | number;

target = '홍길동';

target = 13;

// error
target = null;

target = undefined;

// undefined 사용 가능
let targetName: string | undefined;

targetName = '홍길동';

targetName = undefined;
```

하지만 이보다는 물음표 기호(`?`)를 써서 `Optional Parameter`로 처리하는 걸 추천합니다.

### Optional

```typescript
function greeting(name?: string): string {
  return `Hello, ${name || 'world'}`;
}
```

기본값(`=`)을 잡아주면 더 좋습니다.

```typescript
function greeting(name: string = 'world'): string {
  return `Hello, ${name}`;
}
```

매개변수가 오브젝트일 때도 활용할 수 있습니다.

```typescript
function greeting({ name, age }: { name: string; age?: number }): string {
  return age ? `${name} (${age})` : name;
}
```

매개변수에 기본값(`=`)을 잡아주면 더 좋습니다.

```typescript
function greeting(
  { name, age }: { name: string; age?: number } = { name: '' }
): string {
  return age ? `${name} (${age})` : name;
}
```

`타입 별칭`으로 가독성을 높일 수 있습니다.

```typescript
type Human = {
  name: string;
  age?: number;
};

function greeting({ name, age }: Human): string {
  return age ? `${name} (${age})` : name;
}

greeting();

greeting({ name: '홍길동' });

greeting({ name: '홍길동', age: 13 });
```

## Intersection Type

`&`를 사용하여 타입을 확장하는 간단한 방법입니다.

```typescript
type Human = {
  name: string;
  age: number;
};

type Creature = {
  hp: number;
  mp: number;
};

// Intersection Type
type Person = Human & Creature;

let person: Person;

person = { name: '홍길동', age: 13, hp: 256, mp: 16 };
```

## Generics

[제네릭](https://www.typescriptlang.org/ko/docs/handbook/2/generics.html)

```typescript
function identity<Type>(arg: Type): Type {
  return arg;
}
```

## Utility Types

[유틸리티](https://www.typescriptlang.org/ko/docs/handbook/utility-types.html)

## Tips

[더 좋은 타입스크립트 프로그래머로 만드는 11가지 팁](https://velog.io/@lky5697/11-tips-that-help-you-become-a-better-typescript-programmer)

## Visual Studio Code 자동 완성 + 실시간 오류 검사

현실적으로 TypeScript를 쓰는 가장 큰 이유입니다.

오래된 라이브러리의 경우 `d.ts` 파일만 따로 패키지로 제공됩니다.  
패키지 이름은 `@types/블라블라` 형태입니다.

- [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped)
- [DefinitelyTyped/types](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types)
  → 전체 목록은 너무 많아서 GitHub 웹 페이지에서 다 볼 수도 없습니다.
- [DefinitelyTyped/types/react](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/react)
  → React는 여기서 확인할 수 있습니다.  
  React 18이 바로 나오고(`index.d.ts`), 이전 버전은 하위 폴더(디렉터리)로 따로 관리되고 있습니다.
