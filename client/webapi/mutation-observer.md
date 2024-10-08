# Mutation Observer API

`MutationObserver`는 DOM 요소를 관찰하고 객체의 속성 변경이 감지되면 콜백을 실행하는 내장 객체입니다.\
DOM의 변화를 주기적으로 감시한다. DOM의 속성, 텍스트, 자식 노드들에 대한 변경을 감지할 수 있습니다.

## 문법

먼저, 콜백 함수와 함께 옵저버를 생성합니다.
그 다음, DOM 노드에 옵저버를 부착합니다.

```jsx
// 주기적으로 감지할 대상 요소 선정
const node = document.getElementById('id');

// 옵저버 콜백 생성
const callback = (mutationList, observer) => {
  console.log(mutationList);
};

// 옵저버 인스턴스 생성
// node에 변화가 일어나면 콜백함수를 실행
let observer = new MutationObserver(callback);

// DOM의 어떤 부분을 감시할지를 옵션 설정
const config = {
    attributes: true, // 속성 변화 할때 감지
    childList: true, // 자식노드 추가/제거 감지
    characterData: true // 데이터 변경전 내용 기록
};

// 감지 시작
observer.observe(node, config);

// 감지 중지
observer.disconnect();
```

## config

`config`는 "어떤 종류의 변경 사항에 반응할지"를 나타내는 boolean 옵션을 포함한 객체입니다.

- `childList`: 노드의 직접적인 자식 변경을 감지
- `subtree`: 노드의 모든 하위 요소를 감지
- `attributes`: 노드의 속성 변경을 감지
- `attributeFilter`: 선택된 속성 이름만 감지
- `characterData`: 노드의 텍스트 내용을 감지

### 그 외 몇 가지 옵션

- `attributeOldValue`: `true`인 경우, 콜백에 속성의 이전 값과 새로운 값을 모두 전달, 그렇지 않으면 새 값만 전달(속성 감지 옵션 필요)
- `characterDataOldValue`: `true`인 경우, 콜백에 텍스트 데이터의 이전 값과 새로운 값을 모두 전달, 그렇지 않으면 새 값만 전달(텍스트 데이터 감지 옵션 필요)

변경 후에는 콜백이 실행됩니다.\
이때 변경 사항은 첫 번째 인수로 `MutationRecord` 객체 목록에, 옵저버 자체는 두 번째 인수로 전달됩니다.

## MutationRecord 객체의 속성 (MutationObserver 콜백 인자)

### 첫번째 파라미터 mutationList

`callback`은 첫번째 파라미터로 변경 사항을 표현하는 객체 형태의 배열을 받습니다.

```jsx
mutationList = [{
  type: "childList",
  target: <div#elem>,
  removedNodes: [<b>],
  nextSibiling: <text node>,
  oldValue: null,
  previousSibling: <text node>
}, {
  type: "characterData"
  target: <text node>
}];
```

- `type`: 변경사항의 유형, `"attributes"`, `"characterData"`, `"childList"` 중 하나의 값을 가집니다.
  - `"attributes"`: 속성 변경됨
  - `"characterData"`: 텍스트 데이터 변경됨
  - `"childList"`: 자식 요소 추가/제거됨
- `target`: 변경이 발생한  발생한 타겟 노드, `"attributes"` 변경인 경우 요소, `"characterData"` 변경인 경우 텍스트 노드, `"childList"` 변경인 경우 요소
- `addedNodes/removedNodes`: 추가/제거된 노드
- `previousSibling/nextSibling`: 추가/제거된 노드의 previous/next 형제 노드
- `attributeName/attributeNamespace`: 변경된 속성의 이름
- `oldValue`: 변경 이전 값(옵션에 따라 설정된 경우 `attributeOldValue` 또는 `characterDataOldValue` 변경 시에만 사용)

### 두번째 파라미터 observer

두번째 파라미터로는 `observer` 객체 자신을 받을 수 있습니다.

### MutationObserver 옵션 종류

이 옵션의 설정에 따라 mutations이 여러 번 실행될 수 있으므로, 성능 최적화를 위해 가능하면 필요한 것만 정확하게 설정을 부여하는 것이 좋습니다.

속성 | 값 | 설명
:-: | :-:  | :-:
`childList` | `true` / `false` | 대상 노드의 하위 요소의 추가 및 제거를 감시합니다.
`attributes` | `true` / `false` | 대상 노드의 속성에 대한 변화를 감시합니다.
`characterData` | `true` / `false` | 대상 노드의 데이터에 대한 변화를 감시합니다.
`subtree` | `true` / `false` | 대상 노드의 자식 뿐만 아니라 손자 이후로 모두 감시합니다.
`attributeOldValue` | `true` / `false` | 대상 노드의 속성 변경 전의 내용도 기록에 남깁니다.
`characterDataOldValue` | `true` / `false` | 대상 노드의 데이터 변경 전의 내용도 기록에 남깁니다.
`attributeFilter` | `[ "A", "B" ]` | 모든 속성 돌연변이를 관찰 할 필요가 없는 경우 속성 네임 스페이스없이 속성 로컬 이름의 배열로 설정 합니다.

### MutationRecord 사용 예시

예를 들어, `contentEditable` 속성이 있는 `<div>` 요소가 있습니다.\
이 속성은 해당 요소를 포커스하고 수정할 수 있게 합니다

```html
<div contentEditable id="elem">Click and <b>edit</b>, please</div>

<script>
let observer = new MutationObserver(mutationRecords => {
  console.log(mutationRecords); // 변경 사항을 콘솔에 출력
});

// 속성 외의 모든 것을 관찰
observer.observe(elem, {
  childList: true, // 직접적인 자식을 관찰
  subtree: true, // 하위 자식들도 관찰
  characterDataOldValue: true // 이전 텍스트 데이터를 콜백에 전달
});
</script>
```

이 코드를 브라우저에서 실행하고 `<b>edit</b>` 내부의 텍스트를 수정하면, `console.log`에 하나의 변화가 출력됩니다.

```jsx
mutationRecords = [{
  type: "characterData",
  oldValue: "edit",
  target: <text node>,
  // 나머지 속성은 빈 값
}];
```

더 복잡한 편집 작업을 수행하면, 예를 들어 `<b>edit</b>`를 제거하면, 다수의 `MutationRecord`가 포함될 수 있습니다.

```jsx
mutationRecords = [{
  type: "childList",
  target: <div#elem>,
  removedNodes: [<b>],
  nextSibling: <text node>,
  previousSibling: <text node>
}, {
  type: "characterData"
  target: <text node>
}];
```

따라서 `MutationObserver`는 DOM 하위 트리의 모든 변경 사항에 대해 반응할 수 있습니다.

## 통합을 위한 사용 사례

예를 들어, 유용한 기능을 제공하지만 원치 않는 광고를 표시하는 제3자 스크립트가 있다고 가정해봅시다.

```html
<div class="ads">Unwanted ads</div>
```

이 스크립트는 광고를 제거하는 방법을 제공하지 않습니다.\
`MutationObserver`를 사용하면 DOM에 원치 않는 요소가 추가될 때 이를 감지하고 제거할 수 있습니다.
이외에도 제3자 스크립트가 문서에 무언가를 추가할 때 이를 감지하여 페이지를 동적으로 크기를 조절하는 등의 작업을 할 수 있습니다.

## 아키텍처적 사용 사례

`MutationObserver`는 아키텍처 측면에서도 유용한 경우가 있습니다.\
프로그래밍 관련 웹사이트를 만들고 있다고 가정해봅시다. 당연히 기사나 다른 자료들에는 소스 코드 스니펫이 포함될 수 있습니다.\
HTML에 포함된 이러한 스니펫은 다음과 같습니다:

```html
<pre class="language-javascript"><code>
// here's the code
let hello = "world";
</code></pre>
```

이 스니펫을 더 읽기 쉽게 만들고, 동시에 미관상 아름답게 하려면, 사이트에서 `Prism.js`와 같은 JavaScript 문법 강조 라이브러리를 사용할 것입니다.\
Prism에서 문법 강조를 적용하려면 `Prism.highlightElem(pre)`을 호출하여 해당 pre 요소의 내용을 검사하고, 문법 강조를 위해 색상이 입혀진 특수 태그와 스타일을 추가합니다.\
이는 이 페이지의 예시에서 볼 수 있는 것과 유사합니다.

문법 강조를 언제 실행해야 할까요?\
DOM이 준비되는 순간, 즉 `DOMContentLoaded` 이벤트에 맞추어 실행할 수도 있고, 스크립트를 페이지 하단에 넣을 수도 있습니다.\
DOM이 준비되면, `pre[class*="language"]` 요소를 검색하여 `Prism.highlightElem`을 호출할 수 있습니다:

```jsx
// 페이지 내의 모든 코드 스니펫을 강조
document.querySelectorAll('pre[class*="language"]').forEach(Prism.highlightElem);
```

이제까지는 간단하죠? HTML에서 코드 스니펫을 찾아 강조하면 됩니다.

그런데 이제 서버에서 동적으로 자료를 가져와야 하는 상황을 가정해봅시다.\
우리는 나중에 이런 방법을 배우겠지만, 지금 중요한 건 웹 서버에서 HTML 기사를 가져와서 요청에 따라 표시하는 상황입니다:

```jsx
let article = /*fetch new content from server*/
articleElem.innerHTML = article;
```

새로 가져온 HTML 기사에는 코드 스니펫이 포함되어 있을 수 있습니다.\
이런 스니펫은 `Prism.highlightElem`을 호출하지 않으면 문법 강조가 적용되지 않습니다.

동적으로 로드된 기사에 대해 `Prism.highlightElem`을 어디서, 언제 호출해야 할까요?

우리는 기사 로드 코드를 수정하여 이렇게 강조를 추가할 수 있습니다.

```jsx
let article = /*fetch new content from server*/
articleElem.innerHTML = article;

let snippets = articleElem.querySelectorAll('pre[class*="language-"]');
snippets.forEach(Prism.highlightElem);
```

하지만 여러 곳에서 콘텐츠를 로드한다고 가정해 봅시다.\
기사뿐만 아니라 퀴즈, 포럼 게시물 등에서도 콘텐츠를 로드합니다.\
로딩 후 매번 문법 강조를 위해 코드를 추가하는 건 불편합니다.

만약 제3자 모듈이 콘텐츠를 로드하는 경우라면 어떻게 할까요?\
예를 들어, 제3자가 작성한 포럼이 동적으로 콘텐츠를 로드하고 있는데, 이 포럼에도 문법 강조를 추가하고 싶다고 가정합니다.\
아무도 제3자 스크립트를 직접 수정하고 싶지 않을 겁니다.

다행히, 다른 방법이 있습니다.

우리는 `MutationObserver`를 사용하여 페이지에 코드 스니펫이 삽입될 때 이를 자동으로 감지하고, 문법 강조를 적용할 수 있습니다.

따라서 문법 강조 기능을 한 곳에서 처리할 수 있어, 여러 곳에서 통합할 필요가 없어집니다.

## 동적 하이라이트 데모

다음은 실제로 작동하는 예시입니다.

이 코드를 실행하면 아래의 요소를 관찰하면서 코드 스니펫이 나타날 때 자동으로 하이라이트를 적용합니다:

```jsx
let observer = new MutationObserver(mutations => {

  for (let mutation of mutations) {
    // 새로운 노드를 검사하고, 하이라이트할 것이 있는지 확인
    for (let node of mutation.addedNodes) {
      // 요소만 추적하고, 다른 노드(예: 텍스트 노드)는 건너뜀
      if (!(node instanceof HTMLElement)) continue;

      // 삽입된 요소가 코드 스니펫인지 확인
      if (node.matches('pre[class*="language-"]')) {
        Prism.highlightElement(node);
      }

      // 혹시 서브트리에 코드 스니펫이 있는지 확인
      for (let elem of node.querySelectorAll('pre[class*="language-"]')) {
        Prism.highlightElement(elem);
      }
    }
  }

});

let demoElem = document.getElementById('highlight-demo');

// 관찰 시작
observer.observe(demoElem, {childList: true, subtree: true});
```

위 코드를 실행하면 지정된 요소(`highlight-demo`)를 관찰하게 되고, 코드 스니펫이 삽입되면 하이라이트를 적용하게 됩니다.

아래 코드를 실행하여 `innerHTML`을 동적으로 채우면 `MutationObserver`가 반응하여 해당 내용을 하이라이트 처리합니다.

```jsx
let demoElem = document.getElementById('highlight-demo');

// 동적으로 코드 스니펫을 포함한 콘텐츠 삽입
demoElem.innerHTML = `A code snippet is below:
  <pre class="language-javascript"><code> let hello = "world!"; </code></pre>
  <div>Another one:</div>
  <div>
    <pre class="language-css"><code>.class { margin: 5px; } </code></pre>
  </div>
`;
```

이제 `MutationObserver`가 모든 요소 또는 문서 내에서 하이라이트 적용을 추적할 수 있습니다.\
HTML에 코드 스니펫을 추가하거나 제거하더라도, 이를 따로 처리할 필요 없이 자동으로 하이라이트가 적용됩니다.

## 추가 메서드

`MutationObserver`에는 관찰을 중단하는 메서드가 있습니다.

```jsx
observer.disconnect();  // 관찰 중단
```

관찰을 중단할 때, 일부 변경 사항이 아직 처리되지 않았을 가능성이 있습니다. 이런 경우에는 다음 메서드를 사용하여 처리되지 않은 변이 기록을 가져올 수 있습니다.

```jsx
observer.takeRecords();  // 처리되지 않은 변이 기록을 가져옴
```

이 두 가지 메서드를 함께 사용할 수 있습니다:

```jsx
// 처리되지 않은 변이 기록 가져오기
// 관찰을 중단하기 전에 호출해야 합니다.
// 최근에 처리되지 않은 변이 사항이 있는 경우 이를 처리하기 위함입니다.
let mutationRecords = observer.takeRecords();

// 변경 사항 추적 중단
observer.disconnect();
```

`observer.takeRecords()`로 반환된 변이 기록은 처리 대기열에서 제거됩니다.\
따라서, `takeRecords()`로 반환된 기록은 더 이상 콜백 함수에서 처리되지 않습니다.

### 가비지 컬렉션과의 상호작용

`MutationObserver`는 내부적으로 노드에 대해 약한 참조(weak references)를 사용합니다. 즉, DOM에서 노드가 제거되어 더 이상 접근할 수 없는 상태가 되면 가비지 컬렉션이 해당 노드를 자동으로 처리할 수 있습니다.

관찰 대상인 DOM 노드가 있다고 해서 가비지 컬렉션이 방지되는 것은 아닙니다.

## 요약

`MutationObserver`는 DOM의 변경 사항(속성, 텍스트 내용, 요소의 추가/삭제)에 반응할 수 있습니다.

이를 통해 코드의 다른 부분에서 발생한 변경 사항을 추적할 수 있으며, 서드파티 스크립트와 통합할 때도 유용합니다.

`MutationObserver`는 다양한 변경 사항을 추적할 수 있으며, 관찰할 내용을 설정하는 옵션(`config`)을 통해 불필요한 콜백 호출을 줄여 성능 최적화에 도움을 줄 수 있습니다.

## 참고 자료

- [Javascript.info](https://ko.javascript.info/mutation-observer)
- [MutationObserver - DOM의 변화를 감시](https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-MutationObserver-DOM%EC%9D%98-%EB%B3%80%ED%99%94%EB%A5%BC-%EA%B0%90%EC%8B%9C)
