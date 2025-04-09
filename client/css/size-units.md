# 크기 & 단위

## Position과 %

### Position: static과 relative의 %

**가장 가까운 `block` 레벨**(`inline` 제외)의 요소인 조상이 기준이 됩니다.\
(조상은 `padding` 을 제외 `content` 의 크기가 기준)

### Position: absolute의 %

`position` 이 **static이 아닌 가장 가까운 조상**이 기준이 됩니다.\
(조상의 `padding` 까지 포함한 크기가 기준)

### Position: fixed의 %

모든 조상을 무시하고 **viewport**가 기준이 됩니다.\
그렇기 때문에 `Position: fixed` 요소를 `width: 100%`로 했을 때 부모의 `width`를 그대로 가져오지 않습니다.\
이때 `width: inherit`를 사용하면 부모의 `width`를 그대로 가져올 수 있습니다.

## 높이를 100%로 고정할 때의 문제점

어떤 요소의 `width`와 `height`을 `100%`했지만 요소가 페이지가 표시되지 않는 문제가 발생할 때가 있습니다.

### 사전 지식

#### inline-block 요소의 width와 height

`inline-block` 요소에서의 `auto`의 의미는 자식 요소들 크기에 자동으로 맞춤하라는 것입니다.

또한 inline-block의 `width: auto`는 자식 요소의 크기를 기준으로 너비를 설정하는 반면, `width: 100%`는 부모 요소의 크기를 기준으로 너비를 설정합니다.

#### block 요소의 width와 height

`block` 요소는 `inline` 요소와 다르게 작동합니다.
`width: auto`와 `height: auto`를 분리해서 생각해야 합니다.

`block` 요소의 `height: auto`는 `inline-block`과 마찬가지로 **_자식 요소 크기를 기준_** 으로 설정하지만, `width: auto`의 경우에는 **_부모 요소 크기를 기준_** 으로 설정합니다.\
`block` 요소의 자세한 `width: auto`는 `100%(부모요소의 width) - 자식요소의 좌/우 margin` 입니다.

`block` 요소의`height: 100%`는 부모 요소의 height 크기를 기준으로 설정합니다.

### 문제 설명

어떤 요소와 조상 모두가 `position: static` 혹은 `position: relative` 일 때 가장 가까운 조상은 `body`가 됩니다.

어떤 요소가 `display: block` 으로 설정 되있는 경우, `height: 100%`는 `body`의 `height` 크기를 기준으로 설정이되는데, `body`의 `height`는 기본이 `height: auto`이기 때문에 자식 요소인 어떤 요소를 기준으로 설정됩니다.

따라서 body와 어떤 요소는 서로의 크기를 기준으로 잡기 때문에, 약간의 딜레마 같은 문제가 발생합니다.\
이때는 html과 `body` 모두 `height: 100%`를 적용해주면 해결이 됩니다.

```css
html,
body {
  height: 100%;
}
```

해당 요소의 height를 vh로 바꾸는 방법도 있습니다.

```css
.container {
  height: 100vh;
}
```

## width를 100%로 고정할 때 주의점

`width`를 `100%`로 했을 때, `box-sizing`이 `border-box`일지라도 `margin`을 제외한 부분이 부모의 `width` 만큼 커지게 됩니다.\
그렇기 때문에 `margin`을 포함한 `width`는 부모보다 클 수 있기 때문에 가로 스크롤이 생길 수 있습니다.

## 올바른 단위 선택

|                              요소                              |                                  사용 단위                                   |
| :------------------------------------------------------------: | :--------------------------------------------------------------------------: |
| `<head>` 또는 `<body>` 같은 root 요소, HTML 요소의 `font-size` |                            `%` 또는 사용하지 않기                            |
|                      일반적인 `font-size`                      |                                    `rem`                                     |
|                       `pading`, `margin`                       |                                     rem                                      |
|                       `border`, `shadow`                       |                                     `px`                                     |
|                       `width`, `height`                        |                     `vh`,`vw` 또는 `position: fixed`와 %                     |
|                `top`, `bottom`, `left`, `right`                | `%` (단, 부모요소가 `height: auto`라면 자식요소는 `%`를 사용할 수 없습니다.) |

## 참고

- [oursmalljoy](https://oursmalljoy.com/css-width-auto-height-auto-%EA%B8%B0%EB%B3%B8%EA%B0%9C%EB%85%90-%EC%9E%98%EB%AA%BB-%EC%83%9D%EA%B0%81%ED%95%98%EA%B3%A0-%EC%9E%88%EB%8A%94-%EA%B2%83%EB%93%A4/)
