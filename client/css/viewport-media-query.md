# 뷰포트 메타 태그 vs 미디어 쿼리

## 뷰포트 메타 태그

뷰포트 메타 태그는 모바일 디바이스에서 웹페이지가 어떻게 보여질지를 정의하는 데 사용됩니다.\
이 태그는 브라우저에게 웹페이지의 초기 크기, 확대/축소 가능 여부, 뷰포트와 컨텐츠 사이의 관계 등을 알려줍니다.\
예를 들어, `<meta name="viewport" content="width=device-width, initial-scale=1.0">`와 같이 설정하면 모바일 장치의 화면 너비에 맞추어 웹페이지를 표시하고 초기 확대/축소 배율을 1로 설정합니다.\
이를 통해 모바일 장치에서도 웹페이지가 적절하게 표시될 수 있도록 도와줍니다.

- 뷰포트 속성 (`name="viewport"`)
  : 메타 태그로 하여금 웹사이트를 보여 주는 기기의 브라우저 내 영역, 즉 뷰포트를 대상으로 삼으라고 지시합니다.

- 콘텐츠 속성 (`content="width=device-width`)
  : 사용자에게 실제로 보이는 부분인 뷰포트의 페이지 너비를 설정할 수 있습니다.

  - 초기 화면 배율 (`initial-scale=1.0"`)
    : 초기 화면 배율은 줌 레벨을 정의합니다.
    1.0이면 기본값이므로 줌 기능이 적용되지 않은 것이며 숫자가 커지면 값에 따라 웹사이트가 줌 인 됩니다.

  - 줌 인과 줌 아웃 정보 지정(`user-scalable=yes`)
    : 줌 기능을 제한하고 싶다면 `user-scalable`을 `no`로 지정하면 됩니다.
    기본값은 `yes`이기 때문에 해당 부분을 아예 사용하지 않아도 됩니다.

  - 우선 최대 배율(`Maximum scale=1.0`)
    : 최대 배율은 최대 줌 인 레벨을 제한합니다.

  - 최소 배율(`minimum-scale=1.0`)
    : 최소 배율은 줌 아웃 레벨에 한계를 설정합니다.

## 미디어 쿼리

미디어 쿼리는 CSS에서 사용되며, 미디어 유형 및 특정 조건에 따라 스타일을 적용하거나 변경하는 데 사용됩니다.\
이를 통해 디바이스의 특정 특성에 따라 스타일을 다르게 정의할 수 있습니다.\
미디어 쿼리를 사용하면 반응형 디자인을 쉽게 구현할 수 있으며, 다양한 디바이스 크기와 유형에 따라 적절한 레이아웃을 제공할 수 있습니다.

```css
@media media-type and (media-feature-rule) {
  /* CSS rules go here */
}

/* media-type 생략 가능 */
@media (media-feature-rule) {
  /* CSS rules go here */
}
```

```css
/* 두 가지 조건이 모두 충족될 때 */
@media (media-feature-rule) and (media-feature-rule) {
  /* CSS rules go here */
}
```

```css
/* 두 가지 조건중 하나라도 충족될 때 */
@media (media-feature-rule), (media-feature-rule) {
  /* CSS rules go here */
}
```

### Media types

미디어 타입(Media types)은 CSS에서 사용되는 미디어 유형을 지정하는 데 사용됩니다.\
미디어 타입은 어떤 장치나 출력 매체가 스타일을 해석하고 표시하는 방식을 정의합니다.

- `all`: 기본값으로, 모든 미디어 유형에 적용됩니다.

- `screen`: 컴퓨터 화면, 타블렛, 스마트폰과 같은 스크린 기반의 디바이스에 적용됩니다.

- `print`: 인쇄용 스타일링에 사용됩니다. 인쇄 전용 스타일을 정의할 때 유용합니다.

### Media feature rules

미디어 피처(Media feature)는 CSS의 미디어 쿼리에서 사용되며, 특정 미디어 유형의 속성을 정의하여 스타일을 적용하거나 제한하는 데 사용됩니다.\
미디어 쿼리의 일부로 사용되며, 주로 장치의 특성이나 화면의 속성을 기반으로 조건을 정의합니다.

- `width`: 화면의 너비를 기준으로 스타일을 적용합니다.\
  `min-width`, `max-width`와 함께 사용됩니다.\
  (최소보다 클 때 or 최대보다 작을 때)

  ```css
  @media screen and (max-width: 768px) {
    /* 화면 너비가 768px 이하일 때 적용되는 스타일 */
  }
  ```

- `height`: 화면의 높이를 기준으로 스타일을 적용합니다.\
  `min-height`, `max-height`와 함께 사용됩니다.\
  (최소보다 클 때 or 최대보다 작을 때)

  ```css
  @media screen and (min-height: 600px) {
    /* 화면 높이가 600px 이상일 때 적용되는 스타일 */
  }
  ```

- `orientation`: 장치의 방향(가로/세로)에 따라 스타일을 적용합니다.\
  세로는 `portrait` 가로는 `landscape`을 사용합니다.

  ```css
  @media screen and (orientation: landscape) {
    /* 가로 방향일 때 적용되는 스타일 */
  }
  ```

- `resolution`: 화면 해상도에 따라 스타일을 적용합니다.\
  (최소보다 클 때 or 최대보다 작을 때)

  ```css
  @media screen and (min-resolution: 300dpi) {
    /* 300dpi 이상의 화면 해상도일 때 적용되는 스타일 */
  }
  ```

- `aspect-ratio`: 화면의 가로/세로 비율에 따라 스타일을 적용합니다.

  ```css
  @media screen and (aspect-ratio: 16/9) {
    /* 16:9 비율의 화면일 때 적용되는 스타일 */
  }
  ```

### 미디어 쿼리 사용시 규칙

1. `Media feature rules`에 따라 순서를 올바르게 작성해야 합니다.\
   예를 들어, `min-width: 40rem`와 `min-width: 60rem`을 가진 미디어 쿼리가 있다면 `min-width: 40rem`를 `Media feature rules`로 가진 미디어 쿼리가 더 위에 있어야 합니다.\
   왜냐하면 `40rem`이상이 `60rem`이상보다 범위가 커서 아래쪽 코드가 `40rem`이 되면 `60rem`인 위쪽 코드를 덮어 쓰기 때문입니다.

2. 일반적으로 미디어 쿼리는 코드 맨 아래에 작성하는 것이 일반적입니다.\
   꼭 해야하는 것은 아니지만 이런 컨벤션을 지켜야 다른 개발자들이 코드를 볼 때도 쉽게 파악할 수 있습니다.
