# portals

## createPortal

리액트 앱의 출력 위치를 다르게 설정합니다.\
JSX 코드 렌더링이 현재 어플에서 사용하고 있는 곳이 아니라 웹페이지의 다른 곳에 가야하는 모달이나 비슷한 시나리오가 있을 때 포털이 자주 사용되고 있습니다.

```jsx
ReactDOM.createPortal(child, container)
```

첫 번째 인수는 JSX 코드입니다.\
두 번째 인수는 코드가 옮겨질 HTML 요소입니다.

### 예시

`dialog` 코드를 `id`가 modal인 곳으로 옮김

```jsx
return createPortal(
  <dialog ref={dialog} className="result-modal" onClose={onReset}>
    {userLost && <h2>You lost</h2>}
    {!userLost && <h2>Your Scores: {score}</h2>}
    <p>The target time was <strong>{targetTime} seconds</strong></p>
    <p>
      You stopped the timer with
      <strong>{formattedRemainingTime}</strong>
      seconds left.
    </p>
    <form method="dialog" onSubmit={onReset}>
      <button>Close</button>
    </form>
  </dialog>,
  document.getElementById('modal')
)
```

```html
<body>
  <div id="modal"></div>
  <div id="content">
    <header>
      <h1>The <em>Almost</em> Final Countdown</h1>
      <p>Stop the timer once you estimate that time is (almost) up</p>
    </header>
    <div id="root"></div>
  </div>
  <script type="module" src="/src/main.jsx"></script>
</body>
```
