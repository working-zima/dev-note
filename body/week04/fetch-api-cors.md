# 2. Fetch API & CORS

## 학습 키워드

- Fetch API 란
- Promise
- ReableStream
- Unicode
- CORS 란

## Fetch API

### Streams API

Streams API는 Javascript를 이용해 네트워크를 통해 전송된 데이터 스트림에 접근하여 원하는 대로 처리가 가능한 API를 제공합니다.

Streaming은 네트워크를 통해 받은 리소스를 작은 조각으로 나누어, Bit 단위로 처리합니다.\
Stream의 주요한 기본 사용법은 응답 데이터를 stream으로 만드는 것입니다.

#### Stream

데이터의 흐름을 나타냅니다.\
여러 개의 데이터 청크(chunk)로 구성되어 있고, 비동기적으로 전달될 수 있습니다.\
Stream은 읽기, 쓰기, 변환 등 다양한 작업을 수행할 수 있습니다.

`fetch()`를 통해 정상적으로 전송된 응답 `Body`는 ReadableStream로 표현 가능합니다.\
또한 `ReadableStream.getReader()`를 통해 Reader 객체를 얻어 데이터를 읽을 수도 있으며, `ReadableStream.cancel()`로 Stream을 취소하는 것 등이 가능합니다.

#### ReableStream

데이터를 읽는 데 사용됩니다.\
파일에서 데이터를 읽거나 네트워크에서 데이터를 수신하는 등의 작업을 처리할 수 있습니다.

#### Reader 객체

`ReadableStream` 인터페이스에 속하는 객체입니다.\
Reader는 `getReader()` 메서드를 통해 생성되며, Reader 객체는 `read()`를 사용하여 스트림에서 데이터를 읽을 수 있습니다.

#### Chunk

Stream에서 전송되는 데이터의 작은 조각을 나타냅니다.\
`read()` 메서드를 호출할 때마다 스트림에서 새로운 "chunk"가 읽힐 수 있습니다.
"chunk"의 중요한 특성 중 하나는 `done` 속성을 통해 스트림이 끝에 도달했는지 여부를 나타낸다는 것입니다.

### 기본적인 사용법 실험

```jsx
fetch("http://localhost:3000/products");
// → Promise

await fetch("http://localhost:3000/products");
// → Response

const response = await fetch("http://localhost:3000/products");
// → response.body는 ReadableStream

const reader = response.body.getReader();

const chunk = await reader.read();
// → chunk.value는 Uint8Array 타입.
// → 원래는 chunk.done이 true일 때까지 반복해야 한다.

const body = new TextDecoder().decode(chunk.value);

const data = JSON.parse(body);
```

JSON을 기본 지원합니다.

```jsx
const response = await fetch("http://localhost:3000/products");
const data = await response.json();
```

다른 HTTP Method를 쓰고 싶다면?

```jsx
const response = fetch(url, {
  method: "POST",
});
```

## CORS

웹 브라우저는 Same Origin Policy에 따라 웹 페이지와 리소스를 요청한 곳(여기서는 REST API 서버)이 서로 다른 출처(포트번호까지 포함)일 때 서버에서 얻은 결과를 사용할 수 없게 막습니다.\
CORS오류가 발생하여도 데이터를 서버에 요청하고 응답을 받아오는 것까지는 이미 진행이 다 된 상황이란 점에 주의해 주세요.

REST API 서버(resource를 주는 곳)에서 `Headers`에 “Access-Control-Allow-Origin” 속성을 추가하면 됩니다.

### 패키지 설치

Express에선 [CORS 미들웨어](https://expressjs.com/en/resources/middleware/cors.html)를 설치해서 사용하면 됩니다.

```bash
npm i cors
npm i -D @types/cors
```

### CORS 미들웨어 사용

```jsx
import express from 'express';
**import cors from 'cors';**

const app = express();

**app.use(cors());**
```

정교한 설정은 공식 문서 참고.

## 참고 자료

[Fetch API](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API)\
[Fetch 사용하기](https://developer.mozilla.org/ko/docs/Web/API/Fetch_API/Using_Fetch)\
[ReadableStream](https://developer.mozilla.org/ko/docs/Web/API/ReadableStream)\
[자바스크립트에서 데이터 스트림 읽기 (ReadableStream)](https://www.daleseo.com/js-readable-stream/)\
[텍스트 디코더와 텍스트 인코더](https://ko.javascript.info/text-decoder)\
[동일 출처 정책](https://developer.mozilla.org/ko/docs/Web/Security/Same-origin_policy)\
[CORS](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)
