# 1. routing

## 학습 키워드

- HTML DOM API
  - Location
  - pathname

## HTML DOM API

### Location

Location 인터페이스는 객체가 연결된 장소(URL)를 표현하며 현재 URL을 읽을 수 있게 해주고 새로운 URL로 변경(redirect)할 수 있게 해줍니다.

#### 프로퍼티

Location 인터페이스는 아무 속성도 상속하지 않지만, URLUtils의 속성을 구현합니다.

- `Location.href`\
: 온전한 URL을 값으로 하는 DOMString입니다.\
바뀔 경우 연결된 문서도 새로운 페이지로 이동합니다.\
연결된 문서와 다른 오리진에서도 설정할 수 있습니다.

- `Location.protocol`\
: URL의 프로토콜 부분을 값으로 하는 DOMString으로, 마지막의 ':'도 포함합니다.

- `Location.host`\
URL의 호스트 부분을 값으로 하는 DOMString으로, 호스트명, ':', 포트 번호를 포함합니다.

- `Location.hostname`\
URL의 도메인 부분을 값으로 하는 DOMString입니다.

- `Location.port`\
URL의 포트 번호를 값으로 하는 DOMString입니다.

- `Location.pathname`\
'/' 문자 뒤 URL의 경로를 값으로 하는 DOMString입니다.

- `Location.search`\
'?' 문자 뒤 URL의 쿼리스트링을 값으로 하는 DOMString입니다.\
모던 브라우저에서는 URLSearchParams.get()과 URL.searchParams를 사용해서 인자를 쉽게 추출할 수 있습니다.

- `Location.hash`\
'#' 문자 뒤 URL의 프래그먼트 식별자를 값으로 하는 DOMString입니다.

- `Location.username`\
도메인 이름 이전에 명시된 사용자명을 값으로 하는 DOMString입니다.

- `Location.password`\
도메인 이름 이전에 명시된 비밀번호를 값으로 하는 DOMString입니다.

- `Location.origin` 읽기 전용\
지정한 장소 오리진의 표준 형태를 값으로 하는 DOMString입니다

#### 메서드

Location 인터페이스는 아무 메서드도 상속하지 않지만, URLUtils의 메서드를 구현합니다.

- `Location.assign()`\
주어진 URL의 리소스를 불러옵니다.

- `Location.reload()`\
현재 URL의 리소스를 다시 불러옵니다.\
선택적으로 매개변수에 true를 제공해 브라우저 캐시를 무시하고 서버에서 새로 불러올 수 있습니다,

- `Location.replace()`\
replace() 메소드는 현재 페이지를 새로운 URL로 교체합니다.\
그러나 assign() 메소드와 다르게 replace()를 사용하면 현재 페이지가 세션 기록에 저장되지 않아 뒤로 가기 버튼을 사용하여 해당 페이지로 돌아갈 수 없습니다.

- `Location.toString()`\
전체 URL을 포함하는 DOMString을 반환합니다.\
URLUtils.href와 동의어이지만, 값 수정에는 사용할 수 없습니다.

#### 예제

```jsx
var url = document.createElement("a");
url.href =
  "https://developer.mozilla.org:8080/en-US/search?q=URL#search-results-close-container";
console.log(url.href); // https://developer.mozilla.org:8080/en-US/search?q=URL#search-results-close-container
console.log(url.protocol); // https:
console.log(url.host); // developer.mozilla.org:8080
console.log(url.hostname); // developer.mozilla.org
console.log(url.port); // 8080
console.log(url.pathname); // /en-US/search
console.log(url.search); // ?q=URL
console.log(url.hash); // #search-results-close-container
console.log(url.origin); // https://developer.mozilla.org:8080
```

#### URL

![url-cheatsheet](./img/url-cheatsheet.png)

- `href`\
: 전체 URL을 나타내며 `url.toString()`과 동일합니다.\
이는 리소스의 전체 주소를 나타냅니다.

- `Protocol`\
: 콜론 문자로 끝납니다(예: "https:").\
HTTP 또는 HTTPS와 같이 사용되는 통신 프로토콜을 나타냅니다.

- `Search`\
: 물음표 "?"로 시작하는 매개변수 문자열입니다.\
여기에는 리소스에 대한 추가 정보 또는 매개변수가 포함됩니다.

- `Hash`\
: 해시 문자 "#"으로 시작하고 리소스 내의 특정 섹션이나 위치를 나타냅니다.

- `User and Password properties`\
: `"http://login:password@site.com"`과 같이 HTTP 인증을 사용하는 경우 나타날 수 있습니다.\
그러나 이는 거의 사용되지 않습니다.

## Routing

일반적인 웹 사이트는 URL에 따라 다른 웹 페이지를 보여줍니다.\
하나의 웹 페이지를 하나의 컴포넌트로 만들고, URL에 따라 적절한 컴포넌트가 보이게 함으로써 구현 가능합니다.

```jsx
function App() {
  const path = window.location.pathname;

  // pathname에 따라 다른 컴포넌트 라우팅
  return (
    <div>
      <Header />
      <main>
        {path === '/' && <HomePage />}
        {path === '/about' && <AboutPage />}
      </main>
      <Footer />
    </div>
  );
}
```

page들을 따로 뽑아줄 수도 있습니다.

```tsx
import Header from './components/Header';
import Footer from './components/Footer';

import HomePage from './pages/HomePage';
import AboutPage from './pages/AboutPage';

const pages = {
  '/': HomePage,
  '/about': AboutPage,
};

export default function App() {
  const path = window.location.pathname;

  const Page = Reflect.get(pages, path) || HomePage;

  return (
    <div>
      <Header />
      <main>
        <Page />
      </main>
      <Footer />
    </div>
  );
}
```

## 참고 자료

- [Window.location](https://developer.mozilla.org/ko/docs/Web/API/Window/location)
- [Location](https://developer.mozilla.org/ko/docs/Web/API/Location)
- [URL objects](https://ko.javascript.info/url)
