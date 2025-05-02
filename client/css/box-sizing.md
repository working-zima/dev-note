# 박스 모델, 여백 상쇄

## box-sizing

`box-sizing` 프로퍼티는 `width`, `height` 프로퍼티의 대상 영역을 변경할 수 있습니다.\
`box-sizing` 프로퍼티의 기본값은 `content-box`입니다.

```css
/* box-sizing은 상속이 되지 않음 */
/* 자식 요소 적용하고 싶을 때는 universe selector을 통해 전체 설정 */
* {
  box-sizing: border-box;
}
```

```css
/* margin을 제외한 크기가 기준(border, padding까지 포함) */
box-sizing: border-box;

/* content의 width, height 크기가 기준(border, padding 별도) */
box-sizing: content-box;
```

![box-sizing](./img/box-sizing.png)

## margin collapse(여백 상쇄)

두 개 이상 블록 요소의 `margin` 이 겹칠 때 어느 한 쪽만 적용하는 브라우저 규칙입니다.\
세로로 겹치는 `margin` 에만 적용됩니다.

| 상황          | 발생 조건                                      | 해결 방법                             |
| :------------ | :--------------------------------------------- | :------------------------------------ |
| 형제 요소 간  | 세로로 인접하고 margin 겹칠 때                 | 더 큰 margin 하나만 적용              |
| 부모-자식 간  | 부모가 padding/border 없음                     | 부모에 padding, overflow, border 추가 |
| 빈 block 요소 | 안에 내용 없이 margin-top/bottom 둘 다 있을 때 | 더 큰 margin만 적용됨                 |

### 인접 형제 박스 간 상하 margin이 겹칠 때

인접 형제가 있는 경우 상단 요소의 `margin-bottom` 과 하단 요소의 `margin-top` 중 하나는 상쇄됩니다.
마진의 크기는 두 형제의 `margin` 중 큰쪽으로 적용됩니다.

![인접 형제 박스 간 상하 margin이 겹칠 때](./img/brother.png)

### 부모와 자식의 margin이 겹칠 때

부모가 시각적으로 투명한 상태가 되면 자식과 부모의 `margin` 이 합쳐져 더 큰쪽의 `margin` 값으로 적용됩니다.
(자식의 `margin` 과 부모의 `padding` 이 겹치는게 아님, 착각 금지)

#### 시각적으로 투명한 상태

- `inline` 레벨의 요소에 콘텐츠(텍스트 등)가 없을경우

- 상단, 하단에 명시적으로 `padding` 또는 `border` 값을 주지 않은 경우 등…

![부모와 자식의 margin이 겹칠 때](./img/children.png)
![부모와 자식의 margin이 겹칠 때](./img/children2.png)

#### 부모의 margin이 0일 때, 자식의 margin-top이 부모 바깥으로 튀어나가는 현상

자식 요소가 `margin-top`, `margin-bottom`을 갖고 있고, 부모 요소의 `margin`이 `0`인 경우에 부모가 `margin`이 생긴 것처럼 보이는 현상이 발생할 수 있습니다.\
실제로는 부모의 `margin`이 생긴 것이 아니라, 자식의 `margin`이 부모 바깥으로 튀어나간 margin collapsing 현상 때문입니다.

```html
<div class="parent">
  <div class="child"></div>
</div>
```

```css
.parent {
  margin-top: 0;
  height: 100vh;
  background: black;
}

.child {
  margin-top: 100px;
  height: 100px;
  background: purple;
}
```

이 경우 `.child`의 `margin-top`은 `.parent` 내부에서 적용되지 않고, `.parent` 바깥쪽으로 밀고 나가기 때문에 화면 상단에 검정 배경이 아닌 흰 여백이 나타나는 현상이 생깁니다.

##### 해결 방법

margin collapse가 발생하는 조건은 "두 블록 요소 사이에 아무 것도 없을 때"입니다.\
자식의 `margin`이 부모 바깥으로 빠져나가지 않도록 하려면, 부모 요소에 다음 중 하나라도 지정하면 됩니다.

```css
/* 방법 1: overflow 설정 */
/* overflow는 margin collapsing을 방지하는 BFC 생성 트리거 */
.parent {
  overflow: hidden;
}

/* 방법 2: padding 추가 */
/* padding은 콘텐츠와 박스를 분리하는 공간이기 때문에, margin이 바깥으로 못 나감 */
.parent {
  padding-top: 1px;
}

/* 방법 3: border 추가 */
/* border가 있으면 부모와 자식이 물리적으로 분리된다고 간주함 */
.parent {
  border-top: 1px solid transparent;
}

/* 방법 4: Flex 컨테이너로 변경 */
/* Flex는 애초에 margin collapsing 자체가 일어나지 않는 레이아웃 */
.parent {
  display: flex;
  flex-direction: column;
}
```

이 중 가장 많이 쓰이는 방식은 `overflow: hidden`입니다.\
단, 자식 요소의 `overflow`를 보여줘야 하는 구조라면 `padding`이나 `border`로 해결하는 것이 안전합니다.

### 빈 태그의 상하 margin이 겹칠 때

어떤 태그의 시각적인 요소가 없다면 그 태그의 `margin` 값은 상하의 `margin` 값 중 큰 값이 `margin` 값이 됩니다.

![빈 태그의 상하 margin이 겹칠 때](./img/empty-tag.png)

## 참고

- [Carlos Augusto de Medeir Filho](https://dev.to/camfilho/margin-collapse-explained-by-images-361e)
- [poiemaweb](https://poiemaweb.com/css3-box-model#4-box-sizing-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)
- [Carlos Augusto de Medeir Filho](https://dev.to/camfilho/margin-collapse-explained-by-images-361e)
