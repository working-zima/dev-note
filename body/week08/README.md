# 테스트

## 8주차 학습 목표

### 의미있는 마크업

의미있는 마크업을 하기 위해서는 HTML Element 들이 어떤 의미를 가지고 있는지 정확하게 알고 있어야 합니다.\
[레퍼런스](https://developer.mozilla.org/ko/docs/Web/HTML/Reference)를 참고하여 HTML 마크업에 대해 정확하게 이해한 뒤 정리하고 넘어가시면 좋습니다.

### CSS

CSS 를 잘 사용하기 위해선 CSS 자체에 대해서 잘 알고 있어야 합니다.\
[레퍼런스](https://developer.mozilla.org/ko/docs/Web/CSS/Reference)를 참고하여 CSS 를 정리해봅시다.\
스타일 자체는 굉장히 많기 때문에 자주 사용하는 것들 위주로 찾아서 정리하시면 좋습니다.\
특히나 선택자, 박스모델, media query, [Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/), [Grid](https://css-tricks.com/snippets/css/complete-guide-grid/) 정도는 필수적으로 알고 계셔야 합니다.

### styled-components

유명한 라이브러리들은 대부분 ‘왜’ 만들어졌는지 혹은 품고 있는 철학 등이 잘 정리되어 있는 경우가 많습니다.\
라이브러리를 사용할 때는 [공식문서](https://styled-components.com/docs/basics)를 꼭 살펴보는 습관을 하시는 게 좋습니다.

## 목차

- [Design System](./design-system.md)
- [Style Basics](./style-basics.md)
- [CSS in JS](./css-in-js.md)
- [styled-components](./styled-components.md)
- [props와 attrs](./props-and-attrs.md)
- [Global Style & Theme](./global-style-theme.md)

## 주간 회고

### 배운 점

CSS-in-JS에 대한 전반적인 내용을 학습했습니다.\
styled-components는 CSS-in-JS를 사용하는 대표적인 도구로 배웠습니다.\
styled-components를 사용하면서 여러가지 장점과 단점이 있겠지만, 제 수준에서 체감이 되는 것은 단일 컴포넌트에 관련된 모든 것을 같은 위치에 두는 코로케이션을 쉽게 구현할 수 있는 것입니다.\
이외에도 props를 사용할 수 있고 불필요하게 반복되는 속성도 입력해 놓을 수 있는것이 좋았습니다.\
마지막으로 야샬님은 Global Style 부분이 강점이라고 강조하셨으며 당장 사용하지 않아도 구현해 놓으라는 조언이 있었습니다.

### 고민해야할 점

타입스크립트에 대해 배우고 바로 적용하려고 했지만 배우는 것과 실제로 적용하는 것의 레벨은 컸던 경험이 있습니다.\
아마 CSS-in-JS도 그런 감정을 느끼지 않을까 생각하고, 부지런히 사용해보면 좋을것 같다고 생각합니다.
