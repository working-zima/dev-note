# 2. TypeScript

## 학습 키워드

- REPL
- TypeScript
  - Interface vs Type
  - 타입 추론
  - Union Type vs Intersection Type
  - Optional Parameter

## REPL

간단히 `Read-Eval-Print Loop` 대화형 환경을 쓰고 싶다면 ts-node를 실행하면 됩니다.

```bash
npx ts-node
```

이후 명령을 입력하면 그 명령을 읽고 해석하여 실행한 뒤 결과를 출력합니다.
REPL 환경에서 나가고 싶다면 아래의 키 조합을 입력합니다.

```bash
Ctrl + D

# 또는

Ctrl + C
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

복잡한 오브젝트의 타입을 재사용하기 위해 타입을 정의할 수 있습니다.\
`interface`가 가지는 대부분의 기능은 `type`에서도 동일하게 사용 가능합니다.\
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

여러 타입 중 하나입니다.\
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

레거시 환경 또는 코드 때문에 쓰지 않을 수 없습니다.\
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

`<Type>`으로 타입 변수를 추가하면 `<Type>`은 유저가 준 인수의 타입을 캡처하고 이 정보를 나중에 사용할 수 있게 합니다.

```tsx
function identity<Type>(arg: Type): Type {
  return arg;
}
```

제네릭 identity 함수를 작성하고 나면, 두 가지 방법 중 하나로 호출할 수 있습니다.\

첫 번째 방법은 함수에 타입 인수를 포함한 모든 인수를 전달하는 방법입니다.\
함수를 호출할 때의 인수 중 하나로써 `Type`을 `string` 타입으로 명시해 주었습니다.\
`identity`의 제네릭(`<Type>`)은 `string`이 됩니다.

```tsx
let output = identity<string>("myString"); // 출력 타입은 'string'입니다.
// output: string
```

두 번째 방법은 아마 가장 일반적인 방법입니다. 여기서는 타입 인수 추론 을 사용합니다.\
우리가 전달하는 인수에 따라서 컴파일러가 Type의 값을 자동으로 정하게 하는 것입니다.\
인수인 "myString"을 분석하게 하여 `identity`의 제네릭(`<Type>`)은 분석하고 `string` 타입이 됩니다.

```tsx
let output = identity("myString"); // 출력 타입은 'string'입니다.
// output: string
```

### 제네릭 타입 변수 다루기

제네릭 함수 `loggingIdentity`에서 발생하는 오류는 TypeScript가 모든 타입 Type에 대해 `.length` 속성이 존재한다고 가정하고 있기 때문입니다.\
TypeScript는 제네릭에 대해 어떤 타입이 사용될지를 미리 알 수 없기 때문에, 모든 타입에 대해 `.length` 속성이 있는 것으로 가정하지 않습니다.\
예를 들어, `arg`가 `number` 타입인 경우에는 `.length` 속성이 존재하지 않아서 오류가 발생합니다.

```tsx
function loggingIdentity<Type>(arg: Type): Type {
  console.log(arg.length); // 오류: Type에는 .length 가 없습니다.
  //Property 'length' does not exist on type 'Type'.
  return arg;
}
```

`loggingIdentity` 함수가 Type이 아닌 Type의 배열에서 동작하도록 의도했다고 가정해봅시다.\
 배열로 사용하기 때문에 `.length` 멤버는 사용 가능합니다.\
 다른 타입들의 배열을 만드는 것처럼 표현할 수 있습니다.

```tsx
function loggingIdentity<Type>(arg: Type[]): Type[] {
  console.log(arg.length); // 배열은 .length를 가지고 있습니다. 따라서 오류는 없습니다.
  return arg;
}
```

### 제네릭 타입

제네릭 함수의 타입은 일반적인 함수와 마찬가지로 타입 매개변수가 먼저 나열되어 있으며, 함수 선언과 유사합니다.

```tsx
function identity<Type>(arg: Type): Type {
  return arg;
}

// identity 제네릭 함수의 타입
let myIdentity: <Type>(arg: Type) => Type = identity;
```

또한 제네릭 타입을 객체 리터럴 타입의 call signature로 작성할 수 있습니다.

```tsx
function identity<Type>(arg: Type): Type {
  return arg;
}

let myIdentity: { <Type>(arg: Type): Type } = identity;
```

위와 같은 방법으로 객체 리터럴을 인터페이스로 옮겨보겠습니다.

```tsx
// 인터페이스
interface GenericIdentityFn {
  <Type>(arg: Type): Type;
}

function identity<Type>(arg: Type): Type {
  return arg;
}

// call signature 자리에 인터페이스 사용
let myIdentity: GenericIdentityFn = identity;
```

제네릭 매개변수를 전체 인터페이스의 매개변수로 이동시킬 수도 있습니다.

```tsx
interface GenericIdentityFn<Type> {
  (arg: Type): Type;
}

function identity<Type>(arg: Type): Type {
  return arg;
}

// 매개변수로 number 사용
let myIdentity: GenericIdentityFn<number> = identity;
```

### 제네릭 제약조건

loggingIdentity 예제에서는 arg의 `.length` 속성에 액세스하고자 했지만 컴파일러는 모든 타입이 `.length` 속성을 갖고 있다는 것을 증명할 수 없어 이 가정을 할 수 없다고 경고합니다.

```tsx
function loggingIdentity<Type>(arg: Type): Type {
  console.log(arg.length); // 오류: Type에는 .length 가 없습니다.
  //Property 'length' does not exist on type 'Type'.
  return arg;
}
```

'any'와 모든 타입에서 동작하는 대신에, `.length` 속성을 가진 'any'와 모든 타입들과도 작동하도록 제한하고 싶습니다.\
최소한 `.length` 가 있어야 합니다.\
이를 위해 우리는 Type이 어떤 것일 수 있는지에 대한 제약을 나타내는 인터페이스를 만들어야 합니다.\

인터페이스와 `extends` 키워드를 사용하여 우리의 제약을 표시할 수 있습니다.
`loggingIdentity` 제네릭 함수에 어떤 인자가 올 것인데 '`length: number`을 확장한 형태이다' 라는 말이 됩니다.
이렇게 된다면 다양한 인자가 오지만 `length: number` 을 가지고 있어야 합니다.

```tsx
interface Lengthwise {
  length: number;
}

// `.length`가 없거나 `.length`가 'number'가 아니라면, 에러가 표시됩니다.
function loggingIdentity<Type extends Lengthwise>(arg: Type): Type {
  console.log(arg.length);
  // 이제 .length 속성이 있다는 것을 알기 때문에 더 이상 오류가 발생하지 않습니다.
  return arg;
}

loggingIdentity(3);
// Argument of type 'number' is not assignable to parameter of type 'Lengthwise'.

loggingIdentity({ length: 10, value: 3 });
```

### 제네릭 제약조건에서 타입 매개변수 사용

여기서 `getProperty` 함수의 첫 번째 타입 매개변수는 `Type`이고, 두 번째 타입 매개변수는 `Key`입니다.\
두 번째 타입 매개변수 `Key`는 첫 번째 타입 매개변수 `Type`의 속성 이름들(`keyof Type`) 중 하나여야 합니다.

```tsx
function getProperty<Type, Key extends keyof Type>(obj: Type, key: Key) {
  return obj[key];
}

let x = { a: 1, b: 2, c: 3, d: 4 };

getProperty(x, "a");

getProperty(x, "m");
// Argument of type '"m"' is not assignable to parameter of type '"a" | "b" | "c" | "d"'.
```

#### keyof 타입 연산자

`keyof` 연산자는 객체 타입의 프로퍼티 `key`를 유니온 타입으로 구성해주는 연산자입니다.
아래 타입 `P`는 `“x” | “y”`와 동일한 타입입니다.

```tsx
type Point = { x: number; y: number };
type P = keyof Point;
// type P = "x" | "y"
```

### 제네릭 클래스

제네릭 클래스는 제네릭 인터페이스와 유사한 형태를 가지고 있습니다.\
제네릭 클래스는 클래스 이름 뒤에 꺽쇠 괄호(`< >`) 안에 제네릭 타입 매개변수 목록을 갖습니다.

```tsx
class GenericNumber<NumType> {
  zeroValue: NumType;
  add: (x: NumType, y: NumType) => NumType;
}

// NumType이 number인 GenericNumber 인스턴스
let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function (x, y) {
  return x + y;
};

let stringNumeric = new GenericNumber<string>();
stringNumeric.zeroValue = "";
stringNumeric.add = function (x, y) {
  return x + y;
};

console.log(stringNumeric.add(stringNumeric.zeroValue, "test"));
```

클래스는 정적 측면과 인스턴스 측면 두 가지 타입을 가집니다.\
제네릭 클래스는 정적 측면이 아닌 인스턴스 측면에서만 제네릭이므로, 클래스로 작업할 때 정적 멤버는 클래스의 타입 매개변수를 쓸 수 없습니다.

### 제네릭에서 클래스 타입 사용

타입스크립트에서 제네릭을 사용하여 팩토리를 생성할 때, 클래스 타입을 생성자 함수로 참조하는 것이 필요합니다.\
예를 들면,

```tsx
function create<Type>(c: { new (): Type }): Type {
  return new c();
}
```

```tsx
class BeeKeeper {
  hasMask: boolean;
}

class ZooKeeper {
  nametag: string;
}

class Animal {
  numLegs: number;
}

class Bee extends Animal {
  keeper: BeeKeeper;
}

class Lion extends Animal {
  keeper: ZooKeeper;
}

function createInstance<A extends Animal>(c: new () => A): A {
  return new c();
}

// new Lion으로 인스턴스를 생성하지 않고 createInstance 함수를 통해서 생성
createInstance(Lion).keeper.nametag; // 타입검사!
createInstance(Bee).keeper.hasMask;  // 타입검사!
```

## Utility Types

### `Awaited<Type>`

이 타입은 비동기 함수에서의 `await`나 `Promise`의 `.then()` 메소드와 같은 작업을 모델링하기 위해 만들어졌습니다.\
구체적으로는 이러한 작업들이 `Promise`를 재귀적으로 풀어나가는 방식을 표현합니다

#### 예시

Promise<string>을 받아서 해당 Promise이 resolve된 값인 string으로 변환합니다.

```tsx
type A = Awaited<Promise<string>>;
// A 타입은 string이 됩니다.
```

`Awaited<Promise<Promise<number>>>`은 중첩된 `Promise`를 재귀적으로 풀어내어 최종적으로 number로 변환합니다.

```tsx
type B = Awaited<Promise<Promise<number>>>;
// B 타입은 number이 됩니다.
```

`Awaited<boolean | Promise<number>>`은 `Promise`를 풀어내고 최종적으로 `number` 또는 `boolean` 중 하나의 타입이 됩니다.

```tsx
type C = Awaited<boolean | Promise<number>>;
// C 타입은 number 또는 boolean 중 하나가 됩니다.
```

### `Partial<Type>`

Type 집합의 모든 프로퍼티를 선택적(옵셔널)으로 타입을 생성합니다.

#### Partial 미사용

```tsx
interface User {
  id: number;
  name: string;
  age: number;
  gender: "m" | "f"
}

// age와 gender가 없기 때문에 error
let admin: User = {
  id: 1,
  name: "Bob"
}
```

#### Partial 사용

```tsx
interface User {
  id: number;
  name: string;
  age: number;
  gender: "m" | "f"
}

let admin: Partial<User> = {
  id: 1,
  name: "Bob"
}

// Partial<User>을 한 순간 User는 아래의 모습과 같습니다.

// interface User {
//   id?: number;
//   name?: string;
//   age?: number;
//   gender?: "m" | "f"
// }
```

### `Required<Type>`

Type 집합의 모든 프로퍼티를 필수로 설정한 타입을 생성합니다.\
`Partial`의 반대입니다.

```tsx
interface User {
  id: number;
  name: string;
  age?: number;
}

// Property 'age' is missing in type '{ id: number, name: string }' but required in type 'Required<Props>'.
let admin: Required<User> = {
  id: 1,
  name: "Bob",
}
```

### `Readonly<Type>`

Type 집합의 모든 프로퍼티읽기 전용(`readonly`)으로 설정한 타입을 생성합니다, 즉 생성된 타입의 프로퍼티는 재할당될 수 없습니다.

```tsx
interface User {
  id: number;
  name: string;
  age?: number;
}

let admin: Readonly<User> = {
  id: 1,
  name: "Bob",
}

// Cannot assign to 'id' because it is a read-only property.
admin.id = 4
```

### `Record<Keys,Type>`

타입 Type의 프로퍼티 키의 집합으로 타입을 생성합니다.\
이 유틸리티는 타입의 프로퍼티를 다른 타입에 매핑 시키는데 사용될 수 있습니다.

```tsx
interface PageInfo {
  title: string;
}

type Page = "home" | "about" | "contact";

// nav의 key는 Page 중 하나이고, value의 type은 PageInfo
const nav: Record<Page, PageInfo> = {
  about: { title: "about" },
  contact: { title: "contact" },
  home: { title: "home" },
};

nav.about;
```

```tsx
type Grade = "1" | "2" | "3" | "4"
type Score = "A" | "B" | "C" | "D" | "E"

// score의 key는 Grade이고 value는 Score
const score: Record<Grade, Score> = {
  1: "A",
  2: "C",
  3: "B",
  4: "D",
}
```

### `Pick<Type, Keys>`

Type에서 프로퍼티 Keys의 집합을 선택해 타입을 생성합니다.

```tsx
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

// TodoPreview를 Todo에서 title과 completed만 사용하는 타입으로 바꿈
type TodoPreview = Pick<Todo, "title" | "completed">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};

todo;
```

### `Omit<Type, Keys>`

Pick과 반대로 Type에서 모든 프로퍼티를 선택하고 키를 제거한 타입을 생성합니다.

```tsx
interface Todo {
  title: string;
  description: string;
  completed: boolean;
}

// TodoPreview는 Todo에서 "description"이 제외한 나머지 타입
type TodoPreview = Omit<Todo, "description">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};

todo;
```

### `Exclude<Type, ExcludedUnion>`

ExcludedUnion에 할당할 수 있는 모든 유니온 멤버를 Type에서 제외하여 타입을 생성합니다.\
Omit과의 차이로는 Omit은 프로퍼티를 제거하고 Exclude는 타입을 제거합니다.

```tsx
type T1 = string | number | boolean;

// T1에서 number와 string 타입 제거
type T2 = Exclude<T1, number | string>;
// T2는 boolean
```

### `Extract<Type, Union>`

Union에 할당할 수 있는 모든 유니온 멤버를 Type에서 가져와서 타입을 생성합니다.

```tsx
// "a" | "b" | "c" 중 "a" | "f" 만 가져옴
type T0 = Extract<"a" | "b" | "c", "a" | "f">;
// type T0 = "a"
```

```tsx
// string | number | (() => void) 중 Function인 타입만 가져옴
type T1 = Extract<string | number | (() => void), Function>;
// type T1 = () => void
```

### `NonNullable<Type>`

Type에서 null과 정의되지 않은 것(undefined)을 제외하고 타입을 생성합니다.

```tsx
type T0 = NonNullable<string | number | undefined>;
// type T0 = string | number

type T1 = NonNullable<string[] | null | undefined>;
// type T1 = string[]
```

### `ReturnType<Type>`

함수 Type의 반환(Return) 타입으로 구성된 타입을 생성합니다.

```tsx
function fn(str: string) {
  return str
}

const a: ReturnType<typeof fn> = 'Only string'
const b: ReturnType<typeof fn> = 1234 // TS2322: Type '123' is not assignable to type 'string'.
```

### `Parameters<Type>`

함수 타입 Type의 매개변수에 사용된 타입에서 튜플 타입을 생성합니다.

```tsx
type Zip = { x: number; y: string; z: boolean };
function zip(x: number, y: string, z: boolean): Zip {
   return { x, y, z };
}

type Params = Parameters<typeof zip>
// type Params = [x: number, y: string, z: boolean]
// zip의 매개변수인 x: number, y: string, z: boolean을 타입으로
```

```tsx
function fn(a: string | number, b: boolean) {
   return `[${a}, ${b}]`;
}

type Parameters_Key = Parameters<typeof fn>; // 함수의 매개변수를 타입으로 변환
// type Parameters_Key = [string | number, boolean]
// fn의 매개변수인 a: string | number, b: boolean가 타입으로
```

## Tips

[더 좋은 타입스크립트 프로그래머로 만드는 11가지 팁](https://velog.io/@lky5697/11-tips-that-help-you-become-a-better-typescript-programmer)

## Visual Studio Code 자동 완성 + 실시간 오류 검사

현실적으로 TypeScript를 쓰는 가장 큰 이유입니다.

오래된 라이브러리의 경우 `d.ts` 파일만 따로 패키지로 제공됩니다.\
패키지 이름은 `@types/블라블라` 형태입니다.

- [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped)
- [DefinitelyTyped/types](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types)
  → 전체 목록은 너무 많아서 GitHub 웹 페이지에서 다 볼 수도 없습니다.
- [DefinitelyTyped/types/react](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types/react)
  → React는 여기서 확인할 수 있습니다.\
  React 18이 바로 나오고(`index.d.ts`), 이전 버전은 하위 폴더(디렉터리)로 따로 관리되고 있습니다.

## 참고 자료

- [제네릭](https://www.typescriptlang.org/ko/docs/handbook/2/generics.html)
- [유틸리티](https://www.typescriptlang.org/ko/docs/handbook/utility-types.html)
- [TypeScript #8 유틸리티 타입 Utility Types](https://www.youtube.com/watch?v=IeXZo-JXJjc)
- [한눈에 보는 타입스크립트](https://www.heropy.dev/p/WhqSC8)
- [Inpa Dev 👨‍💻:티스토리](https://inpa.tistory.com/entry/TS-📘-타입스크립트-유틸리티-타입-💯-총정리)
