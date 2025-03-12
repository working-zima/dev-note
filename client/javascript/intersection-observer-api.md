# Intersection Observer API

```js
new IntersectionObserver(callback[, options]);
```

`IntersectionObserver`의 `callback`은 관찰 대상 요소(`target`)의 가시성이 변할 때마다 실행됩니다.

```js
const observer = new IntersectionObserver(
  (entries) => {
    console.log(entries[0]); // #content
    console.log(entriex[0].isIntersecting); // 화면에 보이면 true, 아니면 false
    console.log(entriex[0].target); // 감시 중인 HTMLElement 요소
    console.log(entriex[0].intersectionRatio); // 요소가 보이는 비율 (0~1)
    console.log(entriex[0].boundingClientRect); // 감시 중인 요소의 위치 정보
  },
  { threshold: 1 }
);

const target = document.querySelector("#content");
observer.observe(target);
```

## 참고

- [Intersection Observer 란? 쉬운 설명 | 무한스크롤 (Infinite Scroll), 지연로딩(Lazy Loading)에 유용한 API](https://www.youtube.com/watch?v=OrG1wGgGfcI)
