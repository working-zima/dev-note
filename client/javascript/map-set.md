# Maps와 Sets

자바스크립트에서 객체 리터럴은 자유도가 높아 타인이 보았을 때 의미 전달이 어려울 수 있어서 명시적으로 maps와 sets를 사용하면 역할, 목적이 뚜렷해질 수 있습니다.

## Sets

**주로 중복이 없는 값을 관리할 때 쓰이는 데이터 구조**
일반적인 배열과 유사한 자료구조 입니다.

- ex) 로그인한 사용자가 사용하고 있는 ID 관리 및 추적

### Sets 특징

1. 요소의 순서가 보장되지 않음
2. **중복이 허용되지 않음**
3. 인덱스 기반으로 접근하지 않음
4. 키가 없는 값이 저장됨

### Set 메서드

#### 빈 Set 생성

```javascript
const set = new Set();

console.log(set);
// Set(0) {size: 0}
```

#### new Set()

Set 초기화

```javascript
const set = new Set([1, 2, 3]);

console.log(set);
// Set(3) {1, 2, 3}
```

#### Set 값에 접근하기

set에 접근하기 위해서는 특정 Set 메서드 사용

```javascript
console.log(set[0]);
//undefined
```

#### set.add()

값을 추가하고 set 사진을 반환

```javascript
set.add(value);

console.log(set);
// Set(1) {value}

set.add(value2);

console.log(set);
// Set(2) {value, value2}

set.add(value);

console.log(set);
// Set(2) {value, value2}
// 중복 값은 add 되지 않음
```

#### set.delete()

값을 제거하며 성공하면 true, 아니면 false를 반환

```javascript
set.delete(value);
```

#### set.has()

셋 내에 값이 존재하면 true, 아니면 false를 반환

```javascript
set.has(value);
```

#### set.clear()

셋을 비움

```javascript
set.clear();
```

#### set.size

셋에 몇 개의 값이 있는지 확인

```javascript
set.size;
```

#### set.forEach()

셋의 값을 대상으로 반복 작업 수행

```javascript
set.forEach((value, valueAgain, set) => {});
```

#### set.entries()

entries() 사용시 \[value, value]가 반환되는 이유는 맵과의 호환성으로 추측함

```javascript
for (const enrty of set.entries()) {
  console.log(enrty);
}
//[1,1], [2,2], [3,3]

for (const enrty of set.entries()) {
  console.log(enrty[0]);
}
//1, 2, 3
```

#### 배열의 중복을 제거하고 싶을 때 유용

```javascript
let arr = [1, 2, 3, 2, 3, 4, 2];
console.log(arr);
// (7) [1, 2, 3, 2, 3, 4, 2]

const set = new Set(arr);
console.log(set);
// Set(4) {1, 2, 3, 4}

arr = Array.from(set);
console.log(arr);
// (4) [1, 2, 3 ,4]
```

## Maps

**다양한 자료형을 허용하는 키가 있는 데이터를 관리할 때 쓰이는 데이터 구조**
일반적인 객체와 유사한 자료구조입니다.

### Maps 특징

1. Key-value의 한 쌍으로 이루어진 데이터 집합
2. key 삽입 순서가 보장됨
3. Key의 중복이 허용되지 않음
4. 키와 값이 어떠한 자료형도 될 수 있음
   (객체를 키로 사용 가능)

```javascript
let obj = { name: "ruel" };

let map = new Map();

map.set(obj, "painkiller");

console.log(obj);
// "painkiller"
```

```js
// obj[1]과 obj["1"]은 사실상 동일한 프로퍼티
const obj = {};
obj[1] = "a";
obj["1"] = "b";

console.log(obj); // { '1': 'b' } (덮어쓰기됨)

// Map은 키 타입을 그대로 유지하여 1과 "1"을 다르게 인식
const map = new Map();
map.set(1, "숫자");
map.set("1", "문자열");

console.log(map); // Map(2) { 1 => '숫자', '1' => '문자열' }
```

### Map 메서드

#### new Map()

빈 Map 생성

```javascript
const map = new Map();

map;
// Map(0) {size: 0}
```

#### map.set()

Map 초기화

```javascript
map.set(key, value);
// Map(1) {'key' => 'value'}

map.set(key2, value2);
// Map(2) {'key' => 'value', 'key2' => 'value2'}
```

- 객체등 문자열이 아닌 값을 key와 value로 사용 가능합니다.
  단, 미리 객체를 변수에 담아서 사용해야합니다.

```javascript
const objKey = { key: "key" };
map.set(objKey, value);
// Map(1) {{...} => value}
```

#### map.get()

key에 해당하는 값 반환

```javascript
map.get(key);
// 'value'

map.get(key2);
// 'value2'
```

#### map.has()

key가 존재하면 true, 존재하지 않으면 false 반환

```javascript
map.has(key);
// true

map.has(value);
// false
```

#### map.delete()

key에 해당하는 값을 삭제

```javascript
map.delete(key);
// true

map.delete(value);
// false
```

#### map.size

요소의 개수 반환
(객체에서는 사용하지 못하는 length 메서드를 size로 사용할 수 있습니다.)

```javascript
map.size;
// 2
```

#### map.clear()

map 안의 모든 요소 제거

```javascript
map.clear();
// undefined

map.size;
// 0
```

#### map.keys(), map.values(), map.forEach()

#### 맵의 값을 대상으로 반복 작업 수행

```javascript
for (let enrty of map) {
  console.log(enrty);
  //[key, value]
}

for (let key of map.keys()) {
  console.log(key);
  //key
}

for (let value of map.values()) {
  console.log(value);
  //value
}

map.forEach((v, k) => {
  console.log(v, k);
  // [value, key]
});
```

#### Array.from(map)

배열로 변환

```javascript
const map = new Map()
// Map(0) {size: 0}

map.set('bedi', 3)
// Map(1) {'bedi' => 3}

map.set('donut', 1)
map.set('shakevan', 1)
map.set('andole', 20)
map.set('jun', 20)
//scores: Map(5) {
//  'bedi' => 3,
//  'donut' => 1,
//  'shakevan' => 1,
//  'andole' => 20,
//  'jun' => 20
//}

[...map]
또는
Array.from(map)
//[
//  [ 'bedi', 3 ],
//  [ 'donut', 1 ],
//  [ 'shakevan', 1 ],
//  [ 'andole', 20 ],
//  [ 'jun', 20 ]
//]
```
