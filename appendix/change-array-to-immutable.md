# Array를 Immutable하게 변경하기

## Immutable Array

흔히 하는 실수와 해결책은 Redux 문서의 [Immutable Update Patterns](https://redux.js.org/usage/structuring-reducers/immutable-update-patterns) 문서를 참고하세요.

### Push

```tsx
const a = [1, 2, 3];
const b = [...a, 4];
```

```tsx
function push(xs, value) {
  return [...xs, value];
}
```

### Shift

```tsx
const a = [1, 2, 3];
const [b, ...c] = a;
```

```tsx
function shift(xs) {
  const [head, ...tail] = xs;
  return [head, tail];
}
```

### Pop

```tsx
const a = [1, 2, 3];
const b = a[a.length - 1];
const c = a.slice(0, a.length - 1);
```

```tsx
function pop(xs) {
  return [
    xs[xs.length - 1],
    xs[slice(0, xs.length - 1),
  ];
}
```

### Remove (by value)

```tsx
const a = [1, 2, 3];
const b = a.filter(i => i !== 2);
```

```tsx
function remove(xs, value) {
  return xs.filter(x => x !== value);
}
```

### Remove (by index)

```tsx
const a = [1, 2, 3];
const b = a.filter((_, i) => i !== 1);
```

```tsx
function removeAt(xs, index) {
  return xs.filter((_, i) => i !== index);
}
```

또는

```tsx
const a = [1, 2, 3];
const b = [...a.slice(0, 1), ...a.slice(1 + 1)];
```

```tsx
function removeAt(xs, index) {
  return [...a.slice(0, index), ...a.slice(index + 1)];
}
```

죽어도 `splice`를 쓰고 싶다면...

```tsx
const a = [1, 2, 3];
const b = [...a];
b.splice(1, 1);
```

```tsx
function removeAt(xs, index) {
  const clone = [...xs];
  clone.splice(index, 1);
  return clone;
}
```
