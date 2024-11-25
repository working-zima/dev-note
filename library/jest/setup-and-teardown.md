# Setup and Teardown

테스트를 작성하는 동안 종종 테스트가 수행되기 전에 발생할 필요가 있는 설정 작업이 있고, 테스트가 수행 된 이후 발생해야 할 필요가 있는 마무리 작업이 있습니다.
Jest는 이를 처리하는 헬퍼 함수들을 제공합니다.

## beforeEach, afterEach

```javascript
beforeEach(() => {
  initializeCityDatabase();
});

afterEach(() => {
  clearCityDatabase();
});

test("city database has Vienna", () => {
  expect(isCity("Vienna")).toBeTruthy();
});

test("city database has San Juan", () => {
  expect(isCity("San Juan")).toBeTruthy();
});
```

- `beforeEach()`
  : 각각의 `test`가 실행되기 전에 실행됩니다.\
  예시에서 `initializeCityDatabase()` 메서드는 `'city database has Vienna'` 테스트가 실행되기 전에 한 번, `'city database has San Juan'` 테스트가 실행되기 전에 한 번 실행됩니다.

- `afterEach()`
  : 각각의 `test`가 실행된 후에 실행됩니다.

## beforeAll, afterAll

경우에 따라, 파일의 시작 부분에 한 번만 설정할 필요가 있습니다.\
설정이 비동기인 경우 매번 시간이 오래걸릴수 있기 때문에, 인라인으로 설정 할 수 없습니다.\
Jest는 이 상황을 처리하기 위한 beforeAll과 afterAll을 제공합니다.

```javascript
// fn.js

const fn = {
  connectUserDb: () => {
    return new Promise((res) => {
      setTimeout(() => {
        res({ name: "Mike", age: 30, gender: "male" });
      }, 500);
    });
  },

  disconnectDb: () => {
    return new Promise((res) => {
      setTimeout(() => {
        res();
      }, 500);
    });
  },
};

module.exports = fn;
```

```javascript
// fn.test.js

const fn = require("./fn");

let user;

beforeAll(async () => {
  user = await fn.connectUserDb();
});

afterAll(() => {
  return fn.disconnectDb();
});

test("이름은 Mike", async () => {
  await expect(user.name).toBe("Mike");
});

test("나이는 30", async () => {
  await expect(user.age).toBe(30);
});

test("성별은 남성", async () => {
  await expect(user.gender).toBe("male");
});
```

- `beforeAll()`
  : `test`가 실행되기 전에 실행됩니다.
  예시에서 `connectUserDb()` 메서드는 `'이름은 Mike'` 테스트가 실행되기 전에 한 번 실행됩니다.

- `afterAll()`
  : `test`가 실행된 후에 실행됩니다.
  예시에서 `disconnectDb()` 메서드는 `'성별은 남성'` 테스트가 실행되기 전에 한 번 실행됩니다.

## Scoping

기본적으로, `before`와 `after` 블럭은 파일의 모든 테스트에 적용됩니다.\
`describe()` 블럭을 사용하여 테스트들을 그룹핑할 수도 있습니다.\
`describe()` 블럭 안에 있을 경우, `before`와 `after` 블럭들은 그 `describe()` 블럭 내부의 테스트에만 적용됩니다.

```javascript
const fn = require("./fn");

let user;

beforeEach(async () => {
  user = await fn.connectUserDb();
});

afterEach(() => {
  return fn.disconnectDb();
});

test("이름은 Mike", async () => {
  await expect(user.name).toBe("Mike");
});

test("나이는 30", async () => {
  await expect(user.age).toBe(30);
});

test("성별은 남성", async () => {
  await expect(user.gender).toBe("male");
});

// descirbe로 테스트들을 그룹핑
describe("Car 관련 작업", () => {
  let car;

  beforeEach(async () => {
    car = await fn.connectUserDb();
  });

  afterEach(() => {
    return fn.disconnectDb();
  });

  test("이름은 Mike", async () => {
    await expect(user.brand).toBe("Mike");
  });

  test("나이는 30", async () => {
    await expect(user.name).toBe(30);
  });

  test("성별은 남성", async () => {
    await expect(user.color).toBe("male");
  });
});
```

`before`과 `after`의 실행 순서는 아래와 같습니다.

```javascript
beforeAll(() => console.log("1 - beforeAll")); // 1
beforeEach(() => console.log("1 - beforeEach")); // 2, 6
afterAll(() => console.log("1 - afterAll")); // 12
afterEach(() => console.log("1 - afterEach")); // 4, 10

test("", () => console.log("1 - test")); // 3

describe("Scoped / Nested block", () => {
  beforeAll(() => console.log("2 - beforeAll")); // 5
  beforeEach(() => console.log("2 - beforeEach")); // 7
  afterAll(() => console.log("2 - afterAll")); // 11
  afterEach(() => console.log("2 - afterEach")); // 9

  test("", () => console.log("2 - test")); // 8
});

// 1 - beforeAll
// 1 - beforeEach
// 1 - test
// 1 - afterEach

// 2 - beforeAll
// 1 - beforeEach
// 2 - beforeEach
// 2 - test
// 2 - afterEach
// 1 - afterEach
// 2 - afterAll

// 1 - afterAll
```

## Order of Execution

일단 `describe` 블록이 완료되면 Jest는 수집 단계에서 발견한 순서대로 모든 `test`를 연속적으로 실행하여 각 테스트가 완료되고 정리된 후 다음 단계로 넘어가도록 기다립니다.

```javascript
describe("describe outer", () => {
  console.log("describe outer-a");

  describe("describe inner 1", () => {
    console.log("describe inner 1");

    test("test 1", () => console.log("test 1"));
  });

  console.log("describe outer-b");

  test("test 2", () => console.log("test 2"));

  describe("describe inner 2", () => {
    console.log("describe inner 2");

    test("test 3", () => console.log("test 3"));
  });

  console.log("describe outer-c");
});

// describe outer-a
// describe inner 1
// describe outer-b
// describe inner 2
// describe outer-c
// test 1
// test 2
// test 3
```

## General Advice

테스트가 실패하는 경우, 가장 먼저 확인해야 할 사항 중 하나는 수행할 테스트가 유일할 경우 테스트가 실패하는가의 여부여야 합니다.\
Jest에서 단 하나의 테스트만 수행하기 위해, 임시적으로 그 `test` 명령어를 `test.only()`로 변경하세요.

```javascript
test.only("this will be the only test that runs", () => {
  expect(true).toBe(false);
});

test("this test will not run", () => {
  expect("A").toBe("A");
});
```

`test.skip()`을 사용하면 해당 `test`를 건너 뛸 수도 있습니다.

```javascript
test.('this test will run', () => {
  expect(true).toBe(false);
});

test.skip('this test will not run', () => {
  expect('A').toBe('A');
});
```

## test.each(table)(name, fn, timeout)

```javascript
// index.js

function areAnagrams(first, second) {
  const counter = {};
  for (const ch of first) {
    counter[ch] = (counter[ch] || 0) + 1;
  }
  for (const ch of second) {
    counter[ch] = (counter[ch] || 0) - 1;
  }

  return Object.values(counter).every((cnt) => cnt == 0);
}
```

`areAnagrams()` 함수에 여러 인자를 넣어 테스트 하려면 여러 개의 `test`를 사용해야합니다.

```javascript
// index.test.js

import { areAnagrams } from "./";

test("car and bike are not anagrams", () => {
  expect(areAnagrams("car", "bike")).toBe(false);
});

test("car and arc are anagrams", () => {
  expect(areAnagrams("car", "arc")).toBe(true);
});

test("cat and dog are not anagrams", () => {
  expect(areAnagrams("cat", "dog")).toBe(false);
});

test("cat and act are anagrams", () => {
  expect(areAnagrams("cat", "act")).toBe(true);
});
```

이럴 때 `test.each()`를 사용하면 하나의 `test`만 사용할 수 있습니다.

```javascript
// index.test.js

import { areAnagrams } from "./";

test.each([
  ["cat", "bike", false],
  ["car", "arc", true],
  ["cat", "dog", false],
  ["cat", "act", true],
])("areAnagrams(%s, %s) returns %s", (first, second, expected) => {
  expect(areAnagrams(first, second)).toBe(expected);
```

`test`의 별칭(alias)인 `it`을 통해서도 동일한 방식으로 `test.each()` 함수를 사용할 수 있습니다.

```javascript
// index.test.js

import { areAnagrams } from "./";

describe("areAnagrams()", () => {
  it.each([
    ["cat", "bike", false],
    ["car", "arc", true],
    ["cat", "dog", false],
    ["cat", "act", true],
  ])("areAnagrams(%s, %s) returns %s", (first, second, expected) => {
    expect(areAnagrams(first, second)).toBe(expected);
  });
});
```
