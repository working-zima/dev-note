---
description: React 라이브러리의 프레임워크인 Next.js에 관한 내용입니다.
---

# Next.js

## React와 Next.js의 차이

### React의 Client Side Rendering

![react-rendering](./img/react-rendering.png)

#### React의 장점

![react-after-initial-rendering](./img/react-after-initial-rendering.png)

JS Bundle에 서비스에 접근 가능한 모든 컴포넌트 코드가 존재하기 때문에 초기 접속 과정 이후 서버에 요청 없이 JS 실행으로 페이지를 전환할 수 있습니다.\
따라서 초기 접속 과정 이후 페이지 이동이 매우 빠르고 쾌적합니다.

#### React의 단점

![react-fcp](./img/react-fcp.png)

대신 초기 접속 속도가 느립니다.

- FCP가 3sec 이상일 경우 이탈율 32% 증가
- FCP가 5sec 이상일 경우 이탈율 90% 증가
- FCP가 6sec 이상일 경우 이탈율 106% 증가
- FCP가 10sec 이상일 경우 이탈율 123% 증가

### Next.js를 사용하는 이유

![nextjs-rendering](./img/nextjs-rendering.png)

Nest.js는 렌더링 과정에서 서버측에서 JS로 미리 렌더링합니다.\
바로 상호작용은 되지 않지만 완성된 html을 사전 렌더링하기 때문에 빠른 FCP를 달성하였습니다.\
단, JS Bundle은 현재 페이지에 필요한 JS Bundle만 전달 됩니다.

![nextjs-after-rendering](./img/nextjs-after-rendering.png)

또한 초기 접속 과정 이후에는 React의 방식 그대로 처리하여 빠른 페이지 이동을 그대로 가져갔습니다.

- 화면에 렌더링: HTML 코드를 브라우저가 화면에 그려내는 작업
- JS 렌더링: 자바스크립트 코드(React 컴포넌트)를 HTML로 변환하는 과정
- TTI(Time To Interactive): 상호작용까지 가능해진 시점
- Hydration: JS 코드를 기존 HTML 요소들과 연결

![nextjs-pre-fetching](./img/nextjs-pre-fetching.png)

여기서 JS Bundle은 용량을 줄이기 위해 요청 페이지의 JS Bundle만 전달 됩니다.\
이후 Pre Fetching을 통해 현재 페이지에서 이동할 수 있는 모든 페이지의 JS 코드를 불러옵니다.

## 자료

- [한 입 크기로 잘라먹는 Next.js(15+)](https://www.udemy.com/course/onebite-next/)
