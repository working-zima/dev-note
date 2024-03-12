# 1. routing

## 학습 키워드

- HTML DOM API
  - Location
  - pathname

## Routing

일반적인 웹 사이트는 URL에 따라 다른 웹 페이지를 보여준다.\
하나의 웹 페이지를 하나의 컴포넌트로 만들고, URL에 따라 적절한 컴포넌트가 보이게 함으로써 구현 가능함.

```jsx
function App() {
  const { pathname } = window.location;

  return (
    <div>
      <Header />
      <main>
        {pathname === '/' && <HomePage />}
        {pathname === '/about' && <AboutPage />}
      </main>
      <Footer />
    </div>
  );
}
```

## 참고 자료

- [Window.location](https://developer.mozilla.org/ko/docs/Web/API/Window/location)
- [Location](https://developer.mozilla.org/ko/docs/Web/API/Location)
