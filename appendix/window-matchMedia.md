# window.matchMedia() 메서드

미디어 쿼리를 사용하여 현재 사용자 뷰포트의 속성을 검사하는 데 사용됩니다.\
이를 통해 미디어 쿼리에 맞는 스타일을 동적으로 적용하거나 JavaScript에서 특정 동작을 실행할 수 있습니다.

이 메서드는 주어진 미디어 쿼리 문자열을 받아들여 해당 쿼리에 맞는 결과를 반환하는 미디어 쿼리 객체(`MediaQueryList` 객체)를 반환합니다.\
이 객체에는 미디어 쿼리의 상태를 확인할 수 있는 `matches` 속성과 미디어 쿼리 문자열을 포함하고 있습니다.

## matches 속성

`matches` 속성은 `window.matchMedia()` 메서드로 생성된 미디어 쿼리 결과 객체의 속성 중 하나입니다.\
이 속성은 해당 미디어 쿼리가 현재 문서의 뷰포트에 적용되는지 여부를 나타냅니다.\
미디어 쿼리가 현재 화면 크기에 적합하면 `true`가 되고, 그렇지 않으면 `false`가 됩니다.

## 설명 예시

```css
@media (max-width: 600px) {
  /* 스타일 정의 */
}
```

```jsx
if (matchMedia("screen and (max-width: 600px)").matches) {
  // 600px 이상에서 사용할 JavaScript
} else {
  // 600px 미만에서 사용할 JavaScript
}
```
