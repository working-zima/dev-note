# HttpResponse

`HttpResponse` 클래스는 Fetch API의 `Response`를 대체할 수 있는 클래스입니다. 더 편리하게 응답을 선언할 수 있으며, 기존에는 불가능했던 쿠키 응답 모킹(mocking)과 같은 기능을 지원합니다.

## **왜 기본 `Response`가 아닌가?**

기본 Fetch API의 `Response` 인스턴스를 응답 리졸버에서 사용할 수 있습니다. MSW는 표준 요청 및 응답 API를 기반으로 구축되었기 때문에 언제든지 이를 사용할 수 있습니다.

그러나 `HttpResponse` 클래스는 기본 `Response` 클래스에서 지원하지 않는 쿠키 응답 모킹과 같은 특정 기능을 활성화합니다. 이러한 일관성을 유지하기 위해 `HttpResponse` 클래스를 사용하는 것이 좋습니다. 이 API는 라이브러리 고유의 기능이지만 표준 `Response` 생성자 시그니처와 메서드를 최대한 준수하며, 라이브러리 고유의 기능을 최소화하려고 합니다.

> **역사적 배경** > `Set-Cookie` 헤더가 설정될 때마다 프록시 세터(proxy setter)를 만들기 위해 글로벌 `Response` 및 `Headers` 클래스를 암묵적으로 패치하는 방식이 있었지만, MSW는 글로벌 객체를 수정하지 않기로 했습니다. 이는 응용 프로그램과 실행 환경의 무결성을 존중하고, 표준 API 및 우수 사례에 따라 API 모킹을 구축할 수 있음을 증명하기 위해서입니다.

## **호출 시그니처 (Call Signature)**

`HttpResponse` 클래스는 Fetch API `Response` 클래스와 동일한 생성자 시그니처를 갖습니다. `Response.json()` 및 `Response.error()`와 같은 정적 메서드도 포함됩니다.

```typescript
class HttpResponse {
  constructor(
    body:
      | Blob
      | ArrayBuffer
      | TypedArray
      | DateView
      | FormData
      | ReadableStream
      | URLSearchParams
      | string
      | null
      | undefined,
    options?: {
      status?: number;
      statusText?: string;
      headers?: HeadersInit;
    }
  );
}
```

## 표준 메서드

### new HttpResponse(body, init)

응답 본문과 옵션을 사용하여 새 `Response` 인스턴스를 생성합니다.

```tsx
const response = new HttpResponse("안녕하세요, 세계!");
```

일반적인 `Response` 생성자와 유사하게, 응답 옵션을 제공하여 응답 인스턴스를 사용자 지정할 수 있습니다.

```tsx
// 이는 "new Response()"와 동일합니다.
new HttpResponse("찾을 수 없음", {
  status: 404,
  headers: {
    "Content-Type": "text/plain",
  },
});
```

자세한 내용은 `Response` API를 참고하여 응답 생성 방법을 확인하세요.

### HttpResponse.json(body, init)

JSON 본문을 포함하는 새 응답을 생성하는 정적 메서드입니다.

```tsx
http.get("/resource", () => {
  // 이는 "Response.json(body)"와 동일합니다.
  return HttpResponse.json({
    id: "abc-123",
    title: "현대 테스트 관행",
  });
});
```

### HttpResponse.error()

새 네트워크 오류 응답 인스턴스를 생성하는 정적 메서드입니다.

```tsx
http.get("/resource", () => {
  // 이는 "Response.error()"와 동일합니다.
  return HttpResponse.error();
});
```

> 참고: `HttpResponse.error()` 및 `Response.error()`는 네트워크 오류 응답을 사용자 지정할 수 없습니다. MSW는 이 동작을 따르며, 다양한 요청 클라이언트에서 사용자 지정 네트워크 오류 메시지를 일관성 없이 처리하기 때문입니다(일부는 오류를 노출하고, 일부는 노출하지 않음).

## 사용자 정의 메서드

`HttpResponse` 클래스에는 응답 선언을 간소화하는 사용자 정의 정적 메서드가 포함되어 있습니다. 이러한 메서드는 Fetch API 명세에는 없으며 라이브러리 전용입니다.

### HttpResponse.text(body, init)

`Content-Type: text/plain` 헤더와 지정된 응답 본문을 포함하는 `Response` 인스턴스를 생성합니다.

```tsx
HttpResponse.text("안녕하세요, 세계!");
```

### HttpResponse.html(body, init)

`Content-Type: text/html` 헤더와 지정된 응답 본문을 포함하는 `Response` 인스턴스를 생성합니다.

```tsx
HttpResponse.html(`<p class="greeting">안녕하세요, 세계!</p>`);
```

### HttpResponse.xml(body, init)

`Content-Type: application/xml` 헤더와 지정된 응답 본문을 포함하는 `Response` 인스턴스를 생성합니다.

```tsx
HttpResponse.xml(`
<post>
  <id>abc-123</id>
  <title>현대 테스트 관행</title>
</post>
`);
```

### HttpResponse.formData(body, init)

`Content-Type: multipart/form-data` 헤더와 지정된 응답 본문을 포함하는 `Response` 인스턴스를 생성합니다.

```tsx
const form = new FormData();
form.append("id", "abc-123");
form.append("title", "현대 테스트 관행");

HttpResponse.formData(form);
```

### HttpResponse.arrayBuffer(body, init)

주어진 `ArrayBuffer` 본문으로 새 `Response` 인스턴스를 생성합니다. 버퍼의 바이트 길이에 따라 `Content-Length` 응답 헤더가 자동으로 설정되며, `Content-Type`과 같은 추가 헤더는 설정되지 않습니다.

```tsx
HttpResponse.arrayBuffer(buffer, {
  headers: {
    "Content-Type": "application/octet-stream",
  },
});
```
