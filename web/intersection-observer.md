# Intersection Observer API

```js
new IntersectionObserver(callback[, options]);
```

`Intersection Observer API`는 웹 페이지에서 어떤 요소가 화면에 나타나거나 사라질 때, 또는 화면에 보이는 크기가 변할 때 그걸 감지해서 미리 정해둔 함수를 실행하게 해주는 도구입니다.\
즉, 루트 영역(뷰포트)와 대상 객체의 겹침을 감시하는 인터페이스입니다.

## callback

```js
let callback = (entries, observer) => {
  entries.forEach((entry) => {
    // 각 엔트리는 관찰된 하나의 교차 변화을 설명합니다.
    // 대상 요소:
    //   entry.boundingClientRect
    //   entry.intersectionRatio
    //   entry.intersectionRect
    //   entry.isIntersecting
    //   entry.rootBounds
    //   entry.target
    //   entry.time
  });
};
```

### entries (IntersectionObserverEntry 객체들의 배열)

화면에 나타나거나 사라진 요소들에 대한 정보가 들어 있는 리스트입니다.\
`entries`를 통해 어떤 요소가 보이기 시작했는지, 보이지 않게 되었는지를 확인할 수 있습니다.

```js
const $div = document.createElement('div')

let observer = new IntersectionObserver((entries) => console.log(entries))
observer.observe($div)

/*
[IntersectionObserverEntry]
  0: IntersectionObserverEntry
    boundingClientRect: DOMRectReadOnly {x: 0, y: 0, width: 0, height: 0, top: 0, …}
    intersectionRatio: 0
    intersectionRect: DOMRectReadOnly {x: 0, y: 0, width: 0, height: 0, top: 0, …}
    isIntersecting: false
    isVisible: false
    rootBounds: null
    target: div
    time: 3133.10000000149
    [[Prototype]]: IntersectionObserverEntry
  length: 1
  [[Prototype]]: Array(0)
*/
```

```js
const $div = document.createElement('div')
const $li = document.createElement('li')

let observer = new IntersectionObserver((entries) => console.log(entries))
observer.observe($div)
observer.observe($li)

/*
(2) [IntersectionObserverEntry, IntersectionObserverEntry]
0: IntersectionObserverEntry {time: 964237.5, rootBounds: null, boundingClientRect: DOMRectReadOnly, intersectionRect: DOMRectReadOnly, isIntersecting: false, …}
1: IntersectionObserverEntry {time: 964237.5, rootBounds: null, boundingClientRect: DOMRectReadOnly, intersectionRect: DOMRectReadOnly, isIntersecting: false, …}
length: 2
[[Prototype]]: Array(0)
*/
```

### observer

`IntersectionObserver` 자체를 가리키는 객체입니다.\
이걸 사용하면 어떤 요소를 관찰하고 있는지 추가로 알 수 있고, 필요하면 감지를 멈추게 할 수도 있습니다.\
예를 들어, 이미지가 완전히 화면에 나타났을 때만 작업을 수행하도록 할 수 있습니다.

```js
new IntersectionObserver((observer) => console.log(observer))

/*
IntersectionObserver {root: null, rootMargin: '0px 0px 0px 0px', thresholds: Array(1), delay: 0, trackVisibility: false, …}
  delay: 0
  root: null
  rootMargin: "0px 0px 0px 0px"
  scrollMargin: "0px 0px 0px 0px"
  thresholds: [0]
  trackVisibility: false
  [[Prototype]]: IntersectionObserver
*/
```

## options

### root

어떤 기준으로 요소가 화면에 보이는지 확인할지를 정하는 영역입니다.\
기본적으로 `root`를 설정하지 않으면 브라우저의 화면(viewport)이 기준이 됩니다.\
즉, 대상 요소가 얼마나 보이는지 판별할 때 그 뷰포트 경계로서 바운딩 박스를 가져오는 `Element` 또는 `Document` 객체입니다.\
`root`에 특정 요소를 지정하면, 그 요소 안에서 다른 요소가 보이기 시작하거나 사라지는 걸 감지할 수 있습니다.\
예를 들어, 스크롤 가능한 특정 박스 안에서만 어떤 요소가 보이는지 감지하고 싶을 때 `root`를 설정할 수 있습니다.

### rootMargin

화면에서 요소가 보이기 시작하는 위치를 조정하는 설정입니다.\
이 값으로 화면의 크기를 조금 더 크게 또는 작게 만들어서, 실제로 요소가 화면에 들어오기 전이나 후에 감지를 시작할 수 있습니다.\
기본 값은 '`0px 0px 0px 0px`'입니다.\
예를 들어, `rootMargin`을 '`10px 10px 10px 10px`'로 설정하면, 요소가 화면에 들어오기 10px 전에 감지가 시작됩니다.

### threshold

`0.0`을 지정하면 대상의 픽셀 하나라도 보일 때 대상을 볼 수 있는 것으로 취급하고, `1.0`을 지정하면 대상을 온전히 볼 수 있어야 합니다.\
기본 값은 `0.0`입니다.

## 인스턴스 메서드

### disconnect()

```js
intersectionObserver.disconnect();
```

감시 중인 모든 요소들을 모두 감시하지 않도록 합니다.\
더 이상 요소가 화면에 나타나거나 사라지는 걸 체크하고 싶지 않을 때 사용합니다.

### observe()

```js
IntersectionObserver.observe(targetElement);
```

특정 요소를 감시하기 시작할 때 사용합니다.\
감시할 요소를 이 메서드에 넣으면, 그 요소가 화면에 나타나거나 사라질 때 알려줍니다.

### takeRecords()

```js
intersectionObserverEntries = intersectionObserver.takeRecords();
```

아직 처리되지 않은 감시 결과들을 즉시 가져옵니다.\
`IntersectionObserver`가 감지한 변화들을 바로 확인하고 싶을 때 사용할 수 있습니다.\
감지가 되었지만 아직 콜백 함수가 호출되지 않은 이벤트들을 바로 확인할 수 있습니다.

### unobserve()

```js
IntersectionObserver.unobserve(target);
```

특정 요소에 대해 더 이상 감시하지 않도록 할 때 사용합니다.
`observe()`와 반대로, 특정 요소를 감시 대상에서 빼고 싶을 때 호출합니다.

## IntersectionObserverEntry의 인스턴스 메서드

### boundingClientRect

감시 중인 요소가 화면에서 차지하는 영역에 대한 정보를 담고 있습니다.\
이 값은 해당 요소의 위치와 크기를 나타내며, `top, right, bottom, left, width, height` 등의 정보를 포함합니다.\
쉽게 말해, 화면에서 요소가 어떻게 배치되어 있는지 알려줍니다.

### intersectionRatio

감시 중인 요소가 화면에 얼마나 보이는지를 비율로 나타냅니다.\
값은 `0`에서 `1` 사이의 숫자로 표현되며, `0`이면 요소가 전혀 보이지 않는 상태이고, `1`이면 요소가 완전히 화면에 보이는 상태를 의미합니다.\
예를 들어, intersectionRatio가 0.5라면 요소의 절반이 화면에 보이고 있다는 뜻입니다.

```js
function intersectionCallback(entries) {
  entries.forEach((entry) => {
    entry.target.style.opacity = entry.intersectionRatio;
  });
}
```

### intersectionRect

감시 중인 요소의 실제로 화면에 보이는 부분의 위치와 크기를 나타냅니다.\
`boundingClientRect`와 비슷하지만, 이 값은 요소 중에서 화면에 실제로 보이는 부분만을 나타냅니다.\
`top, right, bottom, left, width, height` 정보를 포함합니다.

```js
function intersectionCallback(entries) {
  entries.forEach((entry) => {
    refreshZones.push({
      element: entry.target,
      rect: entry.intersectionRect,
    });
  });
}
```

### isIntersecting

요소가 화면에 보이는지 여부를 `true` 또는 `false`로 알려줍니다.\
`true`라면 요소의 일부 또는 전부가 화면에 나타난 상태이고, `false`라면 화면에 보이지 않는 상태입니다.\
이 값은 매우 간단하게 요소가 화면에 있는지 없는지 확인하는 데 사용됩니다.

```js
function intersectionCallback(entries) {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      intersectingCount += 1;
    } else {
      intersectingCount -= 1;
    }
  });
}
```

### rootBounds

`root`로 설정된 요소의 위치와 크기를 나타냅니다.\
`root`가 지정되지 않았다면, 이 값은 화면(viewport)의 크기와 위치를 나타내게 됩니다.\
`top, right, bottom, left, width, height` 정보를 포함합니다.

### target

감시되고 있는 특정 요소를 가리킵니다.\
`IntersectionObserver`가 관찰 중인 요소 자체를 의미하며, 콜백 함수에서 어떤 요소가 화면에 나타나거나 사라졌는지 바로 확인할 수 있게 해줍니다.

```js
function intersectionCallback(entries) {
  entries.forEach((entry) => {
    entry.target.style.opacity = entry.intersectionRatio;
  });
}
```

### time

특정 요소가 화면에 나타나거나 사라진 시간을 타임스탬프로 나타냅니다.\
이 값은 `IntersectionObserver`가 변화를 감지한 시점을 밀리초 단위로 나타내며, 애니메이션이나 스크롤 이벤트와 관련해 시간 정보를 필요로 할 때 유용합니다.
