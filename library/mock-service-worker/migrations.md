# 1.x -> 2.x

MSW 2.0 주요 변경 사항 및 마이그레이션 가이드

## 릴리스에 관하여

버전 2.0은 라이브러리 설립 이래 가장 큰 API 변경 사항을 포함하고 있습니다.\
새로운 API와 더불어, ReadableStream 지원, ESM 호환성, 그리고 수많은 버그 수정 등 다양한 기능을 포함하고 있습니다.\
이 가이드는 애플리케이션을 버전 2.0으로 마이그레이션하는 데 도움을 줄 것입니다.\
가이드를 처음부터 끝까지 꼼꼼히 읽어보시길 강력히 권장합니다.

## Codemods

Codemod.com의 팀이 MSW 2.0으로의 마이그레이션을 도와줄 훌륭한 codemods 모음을 준비했습니다.

## 설치

```bash
npm install msw@latest
```

## Breaking changes

### 환경

### Node.js 버전

이번 릴리스에서는 최소 지원 Node.js 버전을 18.0.0으로 설정했습니다.

Node.js 18 이전 버전은 더 이상 지원되지 않습니다. MSW의 다음 버전을 사용하려면 Node.js 18 이상으로 마이그레이션해야 합니다.

### TypeScript 버전

이번 릴리스에서는 최소 지원 TypeScript 버전을 4.7로 설정했습니다. 만약 이전 버전의 TypeScript를 사용 중이라면, MSW를 사용하기 위해 TypeScript 4.7 이상으로 마이그레이션하세요. 작성 시점 기준으로 TypeScript 4.6은 거의 2년 이상 된 버전입니다.

### Imports

### Worker imports

브라우저 쪽 통합과 관련된 모든 것은 이제 `msw/browser` 엔트리포인트에서 내보냅니다. 여기에는 `setupWorker` 함수와 관련 타입 정의가 포함됩니다.

변경 전:

```tsx
import { setupWorker } from "msw";
```

변경 후:

```tsx
import { setupWorker } from "msw/browser";
```

### Response resolver arguments

응답 리졸버 함수는 더 이상 `req`, `res`, `ctx` 인자를 받지 않습니다. 대신, 가로챈 요청에 대한 정보를 담고 있는 단일 객체 인자를 받습니다.

변경 전:

```tsx
rest.get("/resource", (req, res, ctx) => {});
```

변경 후:

```tsx
http.get("/resource", (info) => {});
```

사용한 핸들러 네임스페이스(`http` 또는 `graphql`)에 따라 `info` 객체는 다른 속성을 포함합니다. 요청 정보를 접근하는 방법에 대한 자세한 내용은 **Request changes** 섹션을 참조하세요.

요청 핸들러 네임스페이스의 업데이트된 호출 시그니처에 대해 더 알아보세요:

- **http**
  `http` 네임스페이스 API 참조

- **graphql**
  `graphql` 네임스페이스 API 참조

#### Request changes

#### Request URL

가로챈 요청은 이제 Fetch API의 `Request` 인스턴스로 설명되므로, `request.url` 속성은 더 이상 URL 인스턴스가 아닌 일반 문자열입니다.

변경 전:

```tsx
rest.get("/resource", (req) => {
  const productId = req.url.searchParams.get("id");
});
```

변경 후:

`request.url` 문자열을 `URL` 인스턴스로 사용하려면 문자열을 기반으로 직접 생성해야 합니다.

```tsx
import { http } from "msw";

http.get("/resource", ({ request }) => {
  const url = new URL(request.url);
  const productId = url.searchParams.get("id");
});
```

#### Request params

경로 매개변수(Path parameters)는 더 이상 `req.params`에서 노출되지 않습니다.

변경 전:

```tsx
rest.get("/post/:id", (req) => {
  const { id } = req.params;
});
```

변경 후:

경로 매개변수를 접근하려면 응답 리졸버의 `params` 객체를 사용하세요.

```tsx
import { http } from "msw";

http.get("/post/:id", ({ params }) => {
  const { id } = params;
});
```

#### Request cookies

요청 쿠키는 더 이상 `req.cookies`에서 노출되지 않습니다.

변경 전:

```tsx
rest.get("/resource", (req) => {
  const { token } = req.cookies;
});
```

변경 후:

요청 쿠키를 접근하려면 응답 리졸버의 `cookies` 객체를 사용하세요.

```tsx
import { http } from "msw";

http.get("/resource", ({ cookies }) => {
  const { token } = cookies;
});
```

#### Request body

가로챈 요청 본문(Request body)은 더 이상 `req.body` 속성을 통해 읽을 수 없습니다. Fetch API 명세에 따라 `request.body`는 본문이 설정된 경우 `ReadableStream`을 반환합니다.

변경 전:

```tsx
rest.post("/resource", (req) => {
  // 라이브러리가 요청의 "Content-Type" 헤더를 기준으로
  // JSON 요청 본문을 가정하던 방식.
  const { id } = req.body;
});
```

변경 후:

MSW는 요청 본문 유형을 더 이상 가정하지 않습니다. 대신, 표준 `Request` 메서드 (`.text()`, `.json()`, `.arrayBuffer()` 등)를 사용하여 원하는 형식으로 요청 본문을 읽으세요.

```tsx
import { http } from "msw";

http.post("/user", async ({ request }) => {
  // 요청 본문을 JSON으로 읽습니다.
  const user = await request.json();
  const { id } = user;
});
```

#### Response declaration

모의 응답(Mocked responses)은 더 이상 `res()` 조합 함수를 사용하여 선언하지 않습니다. 우리는 조합 접근 방식에서 벗어나 웹 표준을 따르기로 했습니다.

변경 전:

```tsx
rest.get("/resource", (req, res, ctx) => {
  return res(ctx.json({ id: "abc-123" }));
});
```

변경 후:

모의 응답을 선언하려면 Fetch API `Response` 인스턴스를 생성하여 응답 리졸버에서 반환하세요.

```tsx
import { http } from "msw";

http.get("/resource", () => {
  return new Response(JSON.stringify({ id: "abc-123" }), {
    headers: {
      "Content-Type": "application/json",
    },
  });
});
```

응답 쿠키 모킹 등과 같은 기능을 지원하고 더 간단한 인터페이스를 제공하기 위해, 이제 라이브러리에서는 네이티브 `Response` 클래스를 대체할 수 있는 `HttpResponse` 클래스를 제공합니다.

```tsx
import { http, HttpResponse } from "msw";

export const handlers = [
  http.get("/resource", () => {
    return HttpResponse.json({ id: "abc-123" });
  }),
];
```

새로운 `HttpResponse` API에 대해 자세히 알아보기:

HttpResponse

`HttpResponse` 클래스에 대한 API 참조.

#### `req.passthrough()`

변경 전:

```tsx
rest.get("/resource", (req, res, ctx) => {
  return req.passthrough();
});
```

변경 후:

```tsx
import { http, passthrough } from "msw";

export const handlers = [
  http.get("/resource", () => {
    return passthrough();
  }),
];
```

##### `res.once()`

`res()` 조합 API가 사라졌으므로 `res.once()` 일회성 요청 핸들러 선언도 사라졌습니다.

변경 전:

```tsx
rest.get("/resource", (req, res, ctx) => {
  return res.once(ctx.text("Hello world!"));
});
```

변경 후:

일회성 요청 핸들러를 선언하려면 세 번째 인수로 객체를 제공하고 그 객체의 `once` 속성을 `true`로 설정하세요.

```tsx
import { http, HttpResponse } from "msw";

http.get(
  "/resource",
  () => {
    return new HttpResponse("Hello world!");
  },
  { once: true }
);
```

#### `res.networkError()`

네트워크 오류를 모킹하려면 `HttpResponse.error()` 정적 메서드를 호출하고 이를 응답 리졸버에서 반환하세요.

변경 전:

```tsx
rest.get("/resource", (req, res, ctx) => {
  return res.networkError("Custom error message");
});
```

변경 후:

```tsx
import { http, HttpResponse } from "msw";

http.get("/resource", () => {
  return HttpResponse.error();
});
```

참고: `Response.error()`는 사용자 정의 오류 메시지를 받지 않습니다. 이전에 MSW는 제공된 사용자 정의 오류 메시지를 요청 클라이언트에 강제로 전달하려 했으나, 네트워크 오류 메시지는 요청 클라이언트가 이를 처리하거나 무시할지 결정하는 문제로 신뢰성 있게 작동하지 않았습니다.

#### Context utilities

이번 릴리스에서 `ctx` 유틸리티 객체는 더 이상 사용되지 않으며, 대신 `HttpResponse` 클래스를 사용하여 모의 응답의 속성(상태 코드, 헤더, 본문 등)을 선언합니다.

#### `ctx.status()`

변경 전:

```tsx
rest.get("/resource", (req, res, ctx) => {
  return res(ctx.status(201));
});
```

변경 후:

```tsx
import { http, HttpResponse } from "msw";

http.get("/resource", () => {
  return new HttpResponse(null, {
    status: 201,
  });
});
```

#### `ctx.set()`

변경 전:

```tsx
rest.get("/resource", (req, res, ctx) => {
  return res(ctx.set("X-Custom-Header", "foo"));
});
```

변경 후:

```tsx
import { http, HttpResponse } from "msw";

http.get("/resource", () => {
  return new HttpResponse(null, {
    headers: {
      "X-Custom-Header": "foo",
    },
  });
});
```

표준 `Headers` API에 대해 알아보세요.

#### `ctx.cookie()`

변경 전:

```tsx
rest.get("/resource", (req, res, ctx) => {
  return res(ctx.cookie("token", "abc-123"));
});
```

변경 후:

```tsx
import { http, HttpResponse } from "msw";

http.get("/resource", () => {
  return new HttpResponse(null, {
    headers: {
      "Set-Cookie": "token=abc-123",
    },
  });
});
```

라이브러리는 `HttpResponse` 클래스를 사용하여 응답 쿠키를 모킹할 때 이를 감지할 수 있습니다. 응답 쿠키를 모킹하려면 반드시 `HttpResponse` 클래스를 사용해야 하며, 쿠키를 설정한 후에는 네이티브 `Response` 클래스에서 이를 읽을 수 없습니다.

#### `ctx.body()`

변경 전:

```tsx
rest.get("/resource", (req, res, ctx) => {
  return res(ctx.body("Hello world"), ctx.set("Content-Type", "text/plain"));
});
```

변경 후:

```tsx
import { http, HttpResponse } from "msw";

http.get("/resource", () => {
  return new HttpResponse("Hello world");
});
```

#### `ctx.text()`

변경 전:

```tsx
rest.get("/resource", (req, res, ctx) => {
  return res(ctx.text("Hello world!"));
});
```

변경 후:

```tsx
import { http, HttpResponse } from "msw";

http.get("/resource", () => {
  return new HttpResponse("Hello world!");
});
```

#### `ctx.json()`

변경 전:

```tsx
rest.get("/resource", (req, res, ctx) => {
  return res(ctx.json({ id: "abc-123" }));
});
```

변경 후:

```tsx
import { http, HttpResponse } from "msw";

http.get("/resource", () => {
  return HttpResponse.json({ id: "abc-123" });
});
```

`HttpResponse`의 정적 메서드를 사용할 때 `Content-Type` 응답 헤더를 명시적으로 지정할 필요는 없습니다.

#### `ctx.xml()`

변경 전:

```tsx
rest.get("/resource", (req, res, ctx) => {
  return res(ctx.xml("<foo>bar</foo>"));
});
```

변경 후:

```tsx
import { http, HttpResponse } from "msw";

http.get("/resource", () => {
  return HttpResponse.xml("<foo>bar</foo>");
});
```

#### `ctx.data()`

변경 전:

```tsx
graphql.query("GetUser", (req, res, ctx) => {
  return res(
    ctx.data({
      user: {
        firstName: "John",
      },
    })
  );
});
```

변경 후:

`graphql` 핸들러 네임스페이스는 더 이상 특별한 처리를 받지 않습니다. 대신, 표준 JSON 응답을 직접 선언해야 합니다.

GraphQL 작업을 위한 모의 응답 정의를 더 쉽게 만들기 위해 `HttpResponse.json()` 정적 메서드를 사용하세요:

```tsx
import { graphql, HttpResponse } from "msw";

graphql.query("GetUser", () => {
  return HttpResponse.json({
    data: {
      user: {
        firstName: "John",
      },
    },
  });
});
```

`HttpResponse`를 사용할 때는 응답의 루트 수준 `data` 속성을 명시적으로 포함해야 합니다.

#### `ctx.errors()`

변경 전:

```tsx
graphql.mutation("Login", (req, res, ctx) => {
  const { username } = req.variables;

  return res(
    ctx.errors([
      {
        message: `Failed to login: user "${username}" does not exist`,
      },
    ])
  );
});
```

변경 후:

```tsx
import { graphql, HttpResponse } from "msw";

graphql.mutation("Login", ({ variables }) => {
  const { username } = variables;

  return HttpResponse.json({
    errors: [
      {
        message: `Failed to login: user "${username}" does not exist`,
      },
    ],
  });
});
```

`HttpResponse`를 사용할 때는 `errors` 루트 수준 속성을 명시적으로 포함해야 합니다.

#### `ctx.extensions()`

변경 전:

```tsx
graphql.query("GetUser", (req, res, ctx) => {
  return res(
    ctx.data({
      user: {
        firstName: "John",
      },
    }),
    ctx.extensions({
      requestId: "abc-123",
    })
  );
});
```

변경 후:

```tsx
import { graphql, HttpResponse } from "msw";

graphql.query("GetUser", () => {
  return HttpResponse.json({
    data: {
      user: {
        firstName: "John",
      },
    },
    extensions: {
      requestId: "abc-123",
    },
  });
});
```

#### `ctx.delay()`

변경 전:

```tsx
rest.get("/resource", (req, res, ctx) => {
  return res(ctx.delay(500), ctx.text("Hello world"));
});
```

변경 후:

이제 라이브러리는 `delay()` 함수가 `Promise`를 반환하여 서버 지연을 에뮬레이트할 수 있게 되었으며, 응답 리졸버 내에서 언제든지 이를 `await` 할 수 있습니다.

```tsx
import { http, HttpResponse, delay } from "msw";

http.get("/resource", async () => {
  await delay(500);
  return HttpResponse.text("Hello world");
});
```

`delay()` 함수의 호출 시그니처는 이전 `ctx.delay()`와 동일합니다.

#### `ctx.fetch()`

변경 전:

```tsx
rest.get("/resource", async (req, res, ctx) => {
  const originalResponse = await ctx.fetch(req);
  const originalJson = await originalResponse.json();

  return res(
    ctx.json({
      ...originalJson,
      mocked: true,
    })
  );
});
```

변경 후:

핸들러 내에서 추가 요청을 수행하려면 `msw`에서 새로 추가된 `bypass` 함수를 사용하세요. 이 함수는 주어진 `Request` 인스턴스를 래핑하여 MSW가 해당 요청을 가로채지 않도록 표시합니다.

```tsx
import { http, HttpResponse, bypass } from "msw";

http.get("/resource", async ({ request }) => {
  const originalResponse = await fetch(bypass(request));
  const originalJson = await originalResponse.json();

  return HttpResponse.json({
    ...originalJson,
    mocked: true,
  });
});
```

`bypass` 함수에 대한 API 참조를 확인하세요.

#### `printHandlers()`

`.printHandlers()` 메서드는 더 이상 사용되지 않으며, 대신 `.listHandlers()` 메서드가 사용됩니다.

변경 전:

```tsx
worker.printHandlers();
```

변경 후:

새로운 `.listHandlers()` 메서드는 현재 활성화된 요청 핸들러의 읽기 전용 배열을 반환합니다.

```tsx
worker.listHandlers().forEach((handler) => {
  console.log(handler.info.header);
});
```

#### onUnhandledRequest

`onUnhandledRequest`의 `request` 인자는 이제 추상적인 요청 객체에서 Fetch API의 `Request` 인스턴스로 변경되었습니다. `request.url` 등의 속성을 사용할 때 이 점을 고려해야 합니다.

변경 전:

```tsx
server.listen({
  onUnhandledRequest(request, print) {
    const url = request.url;

    if (url.pathname.includes("/assets/")) {
      return;
    }

    print.warning();
  },
});
```

변경 후:

`request` 인자는 `Request` 인스턴스가 되어, `url` 속성이 이제 `string`입니다.

```tsx
server.listen({
  onUnhandledRequest(request, print) {
    // 새 URL 인스턴스를 수동으로 생성
    const url = new URL(request.url);

    if (url.pathname.includes("/assets/")) {
      return;
    }

    print.warning();
  },
});
```

#### Life-cycle events

이번 릴리스에서는 생명 주기 이벤트 리스너의 호출 시그니처가 변경되었습니다.

변경 전:

```tsx
server.events.on("request:start", (request, requestId) => {});
```

변경 후:

모든 생명 주기 이벤트 리스너는 이제 객체 하나를 인자로 받습니다.

```tsx
server.events.on("request:start", ({ request, requestId }) => {});
```

#### New API

이번 릴리스에서는 파괴적인 변경 사항 외에도 몇 가지 새로운 API가 추가되었습니다. 대부분은 더 이상 사용되지 않는 기능을 호환하기 위한 것입니다.

- `HttpResponse`
- `http`
- `delay()`
- `passthrough()`
- `bypass()`

#### Frequent issues

**`Request`/`Response`/`TextEncoder` is not defined (Jest)**
이 문제는 환경에 Node.js 글로벌 객체들이 정의되지 않아서 발생합니다. 특히 `jest-environment-jsdom`을 사용할 때 발생하는데, 이는 의도적으로 내장 API를 polyfill로 교체하여 Node.js 호환성을 깨뜨립니다.

이 문제를 해결하려면 `jest-environment-jsdom` 대신 `jest-fixed-jsdom`을 사용합니다.

```bash
npm i jest-fixed-jsdom
```

```tsx
// jest.config.js
module.exports = {
  testEnvironment: "jest-fixed-jsdom",
};
```

이 커스텀 환경은 jest-environment-jsdom의 상위 집합으로, 내장 Node.js 모듈을 다시 추가합니다.\
하지만 Jest/JSDOM이 테스트 환경에서 깨뜨리는 부분들이 많아 이를 해결하는 것이 문제일 수 있습니다.\
이 설정은 임시 해결책입니다.

이 설정이 번거롭게 느껴지면, Node.js 글로벌 객체 문제나 ESM 네이티브 지원 문제 없이 사용할 수 있는 최신 테스트 프레임워크인 Vitest로 마이그레이션하는 것을 고려할 수 있습니다.

#### Cannot find module ‘msw/node’ (JSDOM)

이 오류는 테스트 러너가 JSDOM을 기본적으로 브라우저 내보내기 조건을 사용하기 때문에 발생합니다.\
즉, 외부 패키지인 MSW를 가져올 때, JSDOM은 브라우저 내보내기를 엔트리 포인트로 사용합니다.\
이는 잘못된 방법으로, JSDOM은 여전히 Node.js에서 실행되고 있기 때문에 완전한 브라우저 호환성을 보장할 수 없습니다.

이 문제를 해결하려면 `jest.config.js`에서 `testEnvironmentOptions.customExportConditions` 옵션을 `['']`로 설정합니다.

```tsx
// jest.config.js
module.exports = {
  testEnvironmentOptions: {
    customExportConditions: [""],
  },
};
```

이 설정은 JSDOM이 msw/node를 가져올 때 기본 내보내기 조건을 사용하도록 강제하여 올바르게 가져오도록 합니다.

multipart/form-data is not supported Error in Node.js
구버전 Node.js (예: v18.8.0)는 request.formData()에 대한 공식 지원이 없었습니다. 최신 Node.js 18.x 버전으로 업그레이드하면 이러한 지원이 추가되어 해당 문제를 해결할 수 있습니다.

최종 업데이트: 2024년 11월 8일
