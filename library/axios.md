# Axios

## 설치

### npm 사용하기

```bash
npm install axios
```

### yarn 사용하기

```bash
yarn add axios
```

## Fetch vs. Axios

### 공통점

Fetch 와 axios는 모두 promise 기반의 HTTP 클라이언트입니다.\
클라이언트를 이용해 네트워크 요청을 하면 이행(resolve) 혹은 거부(reject)할 수 있는 promise가 반환됩니다.

### 차이점

axios | fetch
:-: | :-:
써드파티 라이브러리로 설치가 필요 | 현대 브라우저에 빌트인이라 설치 필요 없음
XSRF 보호를 해준다. | 별도 보호 없음
data 속성을 사용 | body 속성을 사용
data는 object를 포함한다 | body는 문자열화 되어있다
status가 200이고 statusText가 ‘OK’이면 성공이다 | 응답객체가 ok 속성을 포함하면 성공이다
자동으로 JSON데이터 형식으로 변환된다 | .json()메서드를 사용해야 한다.
요청을 취소할 수 있고 타임아웃을 걸 수 있다. | 해당 기능 존재 하지않음
HTTP 요청을 가로챌수 있음 | 기본적으로 제공하지 않음
download진행에 대해 기본적인 지원을 함 | 지원하지 않음
좀더 많은 브라우저에 지원됨 | Chrome 42+, Firefox 39+, Edge 14+, and Safari 10.1+이상에 지원

#### JSON 데이터 처리

`Fetch`는 객체를 문자열으로 변환한 뒤 `body`에 할당해야 합니다.\
또한 응답 객체의 `.json()` 메서드를 호출하여 `body`의 텍스트를 Promise 형태로 반환해야 합니다.

```tsx
fetch(url, {
  method: "post",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify(todo),
})
  .then((response) => response.json())
  .then((data) => console.log(data));
```

`Axios`는 자동으로 데이터를 문자열로 변환해주며 응답 데이터를 기본적으로 JSON 타입으로 사용할 수 있습니다.

```tsx
axios
  .post(url, {
    headers: {
      "Content-Type": "application/json",
    },
    data: todo,
  })
  .then(console.log);
```

## Axios API

1. `fetch` 메서드처럼 HTTP 메서드 없이 요청할 경우 기본적으로 GET 요청을 생성합니다.

    ```tsx
    axios('/user?ID=12345');
    ```

    두 번째 인자를 사용해서 커스텀 설정하는 것도 가능합니다.

    ```tsx
    axios(url[, config])
    ```

    ```tsx
    axios('/user?ID=12345', {
      method: "get", // 다른 옵션도 가능합니다 (post, put, delete, etc.)
      headers: {},
      data: {},
    });
    ```

    `url`을 두 번째 인자에 사용할 수도 있습니다.

    ```tsx
    axios(config)
    ```

    ```tsx
    axios({
      method: "get",
      url: '/user?ID=12345',
      headers: {},
      data: {},
    });
    ```

    ```tsx
    axios({
      method: 'post',
      url: '/user/12345',
      data: {
        firstName: 'Fred',
        lastName: 'Flintstone'
      }
    });
    ```

2. HTTP 메서드를 붙일 수도 있습니다.

    ```tsx
    axios.request(config)
    axios.get(url[, config])
    axios.delete(url[, config])
    axios.head(url[, config])
    axios.options(url[, config])
    axios.post(url[, data[, config]])
    axios.put(url[, data[, config]])
    axios.patch(url[, data[, config]])
    ```

    ```tsx
    axios.get('/user', {
      params: {
        ID: 12345
      }
    });
    ```

### axios 동시 요청

```tsx
function getUserAccount() {
  return axios.get('/user/12345');
}

function getUserPermissions() {
  return axios.get('/user/12345/permissions');
}

axios.all([getUserAccount(), getUserPermissions()])
  .then(axios.spread(function (acct, perms) {
    // 두개의 요청이 성공했을 때
  }));
```

### axios로 formdata 보내기

```tsx
const addCustomer = () => {
  const url = `/api/customers`;

  const formData = new FormData();
  formData.append("image", file);
  formData.append("name", userName);
  formData.append("birthday", birthday);
  formData.append("gender", gender);
  formData.append("job", job);

  const config = {
    headers: {
      "content-type": "multipart/form-data",
    },
  };

  return axios.post(url, formData, config);
};
```

### 원격 이미지 다운 받기 (blob)

```tsx
const imgurl = 'https://tester.test.com/';

axios({
   url: imgurl,
   method: 'GET',
   responseType: 'blob' // blob 데이터로 이미지 리소스를 받아오게 지정
})
.then((response) => {
   const url = URL.createObjectURL(new Blob([response.data])); // blob 데이터를 객체 url로 변환

   // 이미지 자동 다운 로직
   const link = document.createElement('a');
   link.href = url
   link.setAttribute('download', `sample.png`)
   document.body.appendChild(link)
   link.click()
 })
```

## Axios 인스턴스

```tsx
axios.create([config])
```

```tsx
const instance = axios.create({
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
});
```

## 요청 Config

요청을 만드는 데 사용할 수 있는 config 옵션들 입니다.

```tsx
{
  // `url`은 요청에 사용될 서버 URL입니다.
  url: '/user',

  // `method`는 요청을 생성할때 사용되는 메소드입니다.
  method: 'get', // 기본값

  // `url`이 절대값이 아닌 상대경로인 경우 `baseURL`은 URL 앞에 붙습니다.
  // 상대적인 URL을 인스턴스 메서드에 전달하려면 `baseURL`을 설정하는 것은 편리합니다.
  baseURL: 'https://some-domain.com/api',


  // `transformRequest`는 요청 데이터를 서버로 전송하기 전에 변경할 수 있게 해줍니다.
  // 이것은 'PUT', 'POST', 'PATCH', 'DELETE' 메소드에서만 적용됩니다.
  // 마지막 함수는 Buffer, ArrayBuffer, FormData 또는 Stream의 인스턴스 또는 문자열을 반환해야 합니다.
  // 헤더 객체를 수정할 수 있습니다.
  transformRequest: [function (data, headers) {
    // 데이터를 변환하려는 작업 수행

    return data;
  }],

  // `transformResponse`는 응답 데이터가 then/catch로 전달되기 전에 변경할 수 있게 해줍니다.
  transformResponse: [function (data) {
    // 데이터를 변환하려는 작업 수행

    return data;
  }],

  // `headers`는 사용자 지정 헤더입니다.
  headers: {'X-Requested-With': 'XMLHttpRequest'},

  // `params`은 요청과 함께 전송되는 URL 파라미터입니다.
  // 반드시 일반 객체나 URLSearchParams 객체여야 합니다.
  // 참고: null이나 undefined는 URL에 렌더링되지 않습니다.
  params: {
    ID: 12345
  },

  // `paramsSerializer`는 `params`의 시리얼라이즈를 담당하는 옵션 함수입니다.
  // (예: https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
  paramsSerializer: function (params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },

  // `data`는 요청 바디로 전송될 데이터입니다.
  // 'PUT', 'POST', 'PATCH', 'DELETE' 메소드에서만 적용 가능합니다.
  // `transformRequest`가 설정되지 않은 경우 다음 타입 중 하나여야 합니다.
  // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
  // - 브라우저 전용: FormData, File, Blob
  // - Node 전용: Stream, Buffer
  data: {
    firstName: 'Fred'
  },

  // 바디로 전송하는 데이터의 대안 문법
  // POST 메소드
  // 키가 아닌 값만 전송됩니다.
  data: 'Country=Brasil&City=Belo Horizonte',

  // `timeout`은 요청이 시간 초과되기 전의 시간(밀리초)을 지정합니다.
  // 요청이 `timeout`보다 오래 걸리면 요청이 중단됩니다.
  timeout: 1000, // 기본값은 `0` (타임아웃 없음)

  // `withCredentials`은 자격 증명을 사용하여 사이트 간 액세스 제어 요청을 해야 하는지 여부를 나타냅니다.
  withCredentials: false, // 기본값

  // `adapter`'은 커스텀 핸들링 요청을 처리할 수 있어 테스트가 쉬워집니다.
  // 유효한 Promise 응답을 반환해야 합니다. (lib/adapters/README.md 참고)
  adapter: function (config) {
    /* ... */
  },

  // `auth`는 HTTP Basic 인증이 사용되며, 자격 증명을 제공합니다.
  // `auth`를 사용하면, `Authorization` 헤더가 설정되어 `headers`를 사용하여 설정한 기존의 `Authorization` 사용자 지정 헤더를 덮어씁니다.
  // 이 파라미터를 통해 HTTP Basic 인증만 구성할 수 있음을 참고하세요.
  // Bearer 토큰 등의 경우 `Authorization` 사용자 지정 헤더를 대신 사용합니다.
  auth: {
    username: 'janedoe',
    password: 's00pers3cret'
  },

  // `responseType`은 서버에서 받는 데이터의 타입입니다.
  // 옵션: 'arraybuffer', 'document', 'json', 'text', 'stream'
  // 브라우저 전용: 'blob'
  responseType: 'json', // 기본값

  // `responseEncoding`은 응답 디코딩에 사용할 인코딩입니다.
  // Node.js 전용
  // 참고: 클라이언트 사이드 요청 또는 `responseType`이 'stream'이면 무시합니다.
  responseEncoding: 'utf8', // 기본값

  // `xsrfCookieName`은 xsrf 토큰 값으로 사용할 쿠키의 이름입니다.
  xsrfCookieName: 'XSRF-TOKEN', // 기본값

  // `xsrfHeaderName`은 xsrf 토큰 값을 운반하는 HTTP 헤더의 이름입니다.
  xsrfHeaderName: 'X-XSRF-TOKEN', // 기본값

  // `onUploadProgress`는 업로드 진행 이벤트를 처리합니다.
  // 브라우저 전용
  onUploadProgress: function (progressEvent) {
    // 업로드 진행 이벤트 작업 수행
  },

  // `onDownloadProgress`는 다운로드로드 진행 이벤트를 처리합니다.
  // 브라우저 전용
  onDownloadProgress: function (progressEvent) {
    // 다운로드 진행 이벤트 작업 수행
  },

  // `maxContentLength`는 node.js에서 허용되는 http 응답 콘텐츠의 최대 크기를 바이트 단위로 정의합니다.
  maxContentLength: 2000,

  // `maxBodyLength`는 허용되는 http 요청 콘텐츠의 최대 크기를 바이트 단위로 정의합니다.
  // Node.js 전용
  maxBodyLength: 2000,

  // `validateStatus`는 지정된 HTTP 응답 상태 코드에 대한 Promise를 이행할지 또는 거부할지 여부를 정의합니다.
  // 만약 `validateStatus`가 true를 반환하면(또는 'null' 또는 'undefined'으로 설정) Promise는 이행됩니다.
  // 그렇지 않으면, 그 Promise는 거부될 것이다.
  validateStatus: function (status) {
    return status >= 200 && status < 300; // 기본값
  },

  // `maxRedirects`는 node.js에서 리디렉션 최대값을 정의합니다.
  // 0으로 설정하면 리디렉션되지 않습니다.
  maxRedirects: 5, // 기본값

  // `socketPath`는 node.js에서 사용될 UNIX 소켓을 정의합니다.
  // 예: '/var/run/docker.sock' 도커 데몬에 요청을 보냅니다.
  // 오직 `socketPath` 또는 `proxy`만 지정할 수 있습니다.
  // 둘 다 지정되면 `socketPath`가 사용됩니다.
  socketPath: null, // 기본값

  // `httpAgent`와 `httpsAgent`는 각각 node.js에서 http 및 https 요청을 수행할 때 사용할 사용자 지정 에이전트를 정의합니다.
  // 이렇게 하면 기본적으로 활성화되지 않은 `keepAlive`와 같은 옵션을 추가할 수 있습니다.
  httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),

  // `proxy`는 프록시 서버의 호스트이름, 포트, 프로토콜을 정의합니다.
  // 기존의 `http_proxy`와 `https_proxy` 환경 변수를 사용하여
  // 프록시를 정의할 수도 있습니다.
  // 프록시 구성에 환경 변수를 사용하는 경우, 'no_proxy' 환경 변수를
  // 쉼표로 구분된 프록시가 되지 않는 도메인 목록으로 정의할 수도 있습니다.
  // `false`를 사용하면 프록시를 사용하지 않고, 환경 변수를 무시합니다.
  // `auth`는 프록시에 연결하는데 HTTP Basic auth를 사용해야 함을 나타내며,
  // 자격 증명을 제공합니다. 그러면 `Proxy-Authorization` 헤더가 설정되고,
  // `headers`를 사용하여 기존의 `Proxy-Authorization` 사용자 지정 해더를 덮어씁니다.
  // 만약 프록시 서버가 HTTPS를 사용한다면, 프로토콜을 반드시 `https`로 설정해야 합니다.
  proxy: {
    protocol: 'https',
    host: '127.0.0.1',
    port: 9000,
    auth: {
      username: 'mikeymike',
      password: 'rapunz3l'
    }
  },

  // `cancelToken`은 요청을 취소하는 데 사용할 수 있는 취소 토큰을 지정합니다.
  // (자세한 내용은 요청 취소 섹션 참조)
  cancelToken: new CancelToken(function (cancel) {
  }),

  // `decompress`는 응답 바디의 자동 압축 해제 여부를 나타냅니다.
  //  `true`로 설정하면, 압축 해제된 모든 응답에서 'content-encoding' 헤더도 제거됩니다.
  // Node.js 전용 (XHR은 압축 해제할 수 없습니다)
  decompress: true // 기본값

}
```

## 응답 스키마

```tsx
{
  // `data`는 서버가 제공하는 응답입니다.
  data: {},

  // `status`는 HTTP 상태 코드입니다.
  status: 200,

  // `statusText`는 HTTP 상태 메시지입니다.
  statusText: 'OK',

  // `headers`는 HTTP 헤더입니다.
  // 모든 헤더 이름은 소문자이며, 괄호 표기법을 사용하여 접근할 수 있습니다.
  // 예시: `response.headers['content-type']`
  headers: {},

  // `config`는 요청을 위해 `Axios`가 제공하는 구성입니다.
  config: {},

  // `request`는 이번 응답으로 생성된 요청입니다.
  // 이것은 node.js에서 마지막 ClientRequest 인스턴스 입니다.
  // 브라우저에서는 XMLHttpRequest입니다.
  request: {}
}
```

## 참고

- [Axios Docs](https://axios-http.com/kr/)
- [[번역] 입문자를 위한 Axios vs Fetch](https://velog.io/@eunbinn/Axios-vs-Fetch)
- [AXIOS 설치 & 특징 & 문법 💯 정리](https://inpa.tistory.com/entry/AXIOS-%F0%9F%93%9A-%EC%84%A4%EC%B9%98-%EC%82%AC%EC%9A%A9)
