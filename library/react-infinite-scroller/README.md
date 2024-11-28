# React Infinite Scroller

## Installation

```bash
npm install react-infinite-scroller --save
```

```bash
yarn add react-infinite-scroller
```

## 사용법

```jsx
import InfiniteScroll from "react-infinite-scroller";
```

### Window scroll events

```jsx
<InfiniteScroll
    pageStart={0}
    loadMore={loadFunc}
    hasMore={true || false}
    loader={<div className="loader" key={0}>Loading ...</div>}
>
    {items} // <-- 로드할 컨텐츠
</InfiniteScroll>
```

### DOM scroll events

```jsx
<div style="height:700px;overflow:auto;">
  <InfiniteScroll
    pageStart={0}
    loadMore={loadFunc}
    hasMore={true || false}
    loader={
      <div className="loader" key={0}>
        Loading ...
      </div>
    }
    useWindow={false}
  >
    {items}
  </InfiniteScroll>
</div>
```

### 커스텀 부모 요소

스크롤 계산을 기반으로 할 커스텀 `parentNode` 요소를 정의할 수 있습니다.

```jsx
<div
  style="height:700px;overflow:auto;"
  ref={(ref) => (this.scrollParentRef = ref)}
>
  <div>
    <InfiniteScroll
      pageStart={0}
      loadMore={loadFunc}
      hasMore={true || false}
      loader={
        <div className="loader" key={0}>
          Loading ...
        </div>
      }
      useWindow={false}
      getScrollParent={() => this.scrollParentRef}
    >
      {items}
    </InfiniteScroll>
  </div>
</div>
```

### Props

|       이름        | 필수 여부 |   타입    | 기본값 |                                                                                         설명                                                                                          |
| :---------------: | :-------: | :-------: | :----: | :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|    `children`     |    예     |   Node    |        |                                                                   렌더링 가능한 모든 내용 (PropType의 Node와 동일)                                                                    |
|    `loadMore`     |    예     | Function  |        | 사용자가 더 많은 항목을 요청할 때 호출되는 콜백 함수입니다. 단일 매개변수로 로드할 페이지를 전달받습니다. 예: `function handleLoadMore(page) { /_ 여기에 항목을 더 로드하세요 _/ } }` |
|     `element`     |           | Component | 'div'  |                                                                     컴포넌트가 렌더링할 요소의 이름을 지정합니다.                                                                     |
|     `hasMore`     |           |  Boolean  | false  |                                                  로드할 항목이 더 있는지 여부를 나타냅니다. false일 경우 이벤트 리스너가 제거됩니다.                                                  |
|   `initialLoad`   |           |  Boolean  |  true  |                                                           컴포넌트가 첫 번째 항목 세트를 로드해야 하는지 여부를 나타냅니다.                                                           |
|    `isReverse`    |           |  Boolean  | false  |                                                사용자가 스크롤 가능한 영역의 상단에 도달했을 때 새 항목을 로드할지 여부를 나타냅니다.                                                 |
|     `loader`      |           | Component |        |                               더 많은 항목을 로드하는 동안 렌더링할 React 컴포넌트를 지정합니다. 상위 컴포넌트는 고유한 키(`key`) 속성을 가져야 합니다.                               |
|    `pageStart`    |           |  Number   |   0    |                                                로드할 첫 번째 페이지의 번호를 지정합니다. 기본값이 0인 경우 첫 번째 페이지는 1입니다.                                                 |
| `getScrollParent` |           | Function  |        |                                            InfiniteScroll의 직접 부모가 아닌 경우, 다른 스크롤 리스너를 반환하도록 메서드를 재정의합니다.                                             |
|    `threshold`    |           |  Number   |  250   |                                                            항목 끝에서 이 거리가 되면 `loadMore`를 호출합니다 (픽셀 단위).                                                            |
|   `useCapture`    |           |  Boolean  | false  |                                                             추가된 이벤트 리스너의 `useCapture` 옵션에 대한 프록시입니다.                                                             |
|    `useWindow`    |           |  Boolean  |  true  |                                             스크롤 리스너를 `window`에 추가할지, 아니면 컴포넌트의 `parentNode`에 추가할지를 지정합니다.                                              |

## 문제 해결

### `loadMore`가 두 번 호출되거나 멈추지 않고 호출되는 경우

`loadMore` 콜백이 두 번 호출되거나 계속 호출되는 문제가 발생한다면, 이 컴포넌트를 추가하기 전에 **CSS 레이아웃이 제대로 작동하는지** 확인하세요. 계산은 컨테이너(항목을 감싸는 요소)의 높이를 기준으로 이루어지므로, **컨테이너 높이가 항목 전체의 높이와 동일해야 합니다.**

```css
.my-container {
  overflow: auto;
}
```

일부 사용자들은 `react-infinite-scroll-component`를 사용하는 것이 효과적이었다고 보고했습니다.

### **`isLoading` 속성을 추가하는 게 좋지 않을까요?**

이 컴포넌트는 API 호출 관련 작업에 대해 아무런 가정을 하지 않습니다. 현재 API에서 항목을 로드 중인지 여부를 상태(state) 또는 리듀서(reducers)에 저장하여, **중복된 API 호출이 발생하지 않도록 관리하는 것은 사용자에게 달려 있습니다.**

```jsx
loadMore() {
  if(!this.state.isLoading) {
    this.props.fetchItems();
  }
}
```
