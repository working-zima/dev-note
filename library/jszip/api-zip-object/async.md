# async(type[, onUpdate])

JSZipObject에서 파일 내용을 비동기로 추출합니다.\
`file(...).async()`는 실제로 압축된 바이너리 데이터를 해제(decompress)하는 연산이 실행됩니다.\
지정한 형식(type)에 따라 Promise로 반환되며, Blob, string, Uint8Array, Buffer 등 다양한 형태로 가져올 수 있습니다.

**Returns** : 요청한 형식의 데이터를 담은 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

## Arguments

| name       | type     | description                          |
| ---------- | -------- | ------------------------------------ |
| `type`     | string   | 반환받을 데이터 형식.                |
| `onUpdate` | function | 처리 중마다 호출될 선택적 콜백 함수. |

### type 옵션

`type`에 지정 가능한 값은 다음과 같습니다:

| type 값                  | 설명                                            |
| ------------------------ | ----------------------------------------------- |
| `"string"` 또는 `"text"` | 일반 유니코드 문자열 (`UTF-8`)                  |
| `"base64"`               | base64 인코딩된 문자열                          |
| `"binarystring"`         | 1바이트 바이너리 문자열 (브라우저에서 잘 안 씀) |
| `"uint8array"`           | `Uint8Array` 객체 ( 파일로 저장하기 좋음)       |
| `"array"`                | 0\~255 숫자 배열                                |
| `"arraybuffer"`          | `ArrayBuffer` 객체                              |
| `"blob"`                 | Blob (브라우저에서 다운로드 시 사용)            |
| `"nodebuffer"`           | Node.js 전용 `Buffer` 객체                      |

지원 여부는 [`JSZip.support`]({{site.baseurl}}/documentation/api_jszip/support.html)로 확인 가능

## async 예시

```js
const zip = await JSZip.loadAsync(file);

const imageBytes = await zip.file("image.png")!.async("uint8array");
console.log(imageBytes); // Uint8Array
```

### onUpdate 콜백 예시 (진행률 표시)

데이터 스트림이 내부적으로 처리될 때마다 호출되는 콜백 함수입니다.\
아래의 메타데이터를 인자로 받습니다:

| name    | type   | 설명                            |
| ------- | ------ | ------------------------------- |
| percent | number | 처리 진행률 (0~100 사이의 실수) |

```js
await zip.file("video.mp4")!.async("blob", ({ percent }) => {
  console.log(`진행률: ${percent.toFixed(2)}%`);
});
```

## 오류 핸들링 예시

```js
try {
  const content = await zip.file("data.csv")!.async("string");
  console.log("내용:", content);
} catch (err) {
  console.error("파일 읽기 오류:", err);
}
```

## 유용한 팁

- `"binarystring"`은 UTF-8 문자열이 아니므로 텍스트로 다룰 수 없음

- `"uint8array"`는 일반적으로 파일 다운로드, 이미지 렌더링, WASM 등과 연동할 때 사용

- `"string"`은 텍스트 파일을 읽을 때 가장 많이 사용되는 기본 포맷

- `"blob"`은 브라우저 환경에서 다운로드 링크 생성할 때 적합
