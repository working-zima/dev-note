# async(type[, onUpdate])

지정한 형식으로 파일 내용을 반환하는 Promise를 생성합니다.

**Returns** : 요청한 형식의 데이터를 담은 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

**Since**: v3.0.0

## Arguments

| name     | type     | description                          |
| -------- | -------- | ------------------------------------ |
| type     | string   | 반환받을 데이터 형식.                |
| onUpdate | function | 처리 중마다 호출될 선택적 콜백 함수. |

### type 옵션

`type`에 지정 가능한 값은 다음과 같습니다:

- `base64`: base64 문자열로 반환
- `text` 또는 `string`: 일반 유니코드 문자열로 반환
- `binarystring`: 1바이트 문자로 구성된 바이너리 문자열
- `array`: 숫자 배열 (0~255)
- `uint8array`: Uint8Array 객체 (브라우저 호환 필요)
- `arraybuffer`: ArrayBuffer 객체 (브라우저 호환 필요)
- `blob`: Blob 객체 (브라우저 호환 필요)
- `nodebuffer`: Node.js Buffer 객체 (Node.js 환경 필요)

지원 여부는 [`JSZip.support`]({{site.baseurl}}/documentation/api_jszip/support.html)로 확인 가능

```js
zip
  .file("image.png")
  .async("uint8array")
  .then(function (u8) {
    // u8은 Uint8Array
  });
```

### onUpdate 콜백

데이터 스트림이 내부적으로 처리될 때마다 호출되는 콜백 함수입니다.  
아래의 메타데이터를 인자로 받습니다:

| name    | type   | 설명                            |
| ------- | ------ | ------------------------------- |
| percent | number | 처리 진행률 (0~100 사이의 실수) |

```js
zip
  .file("image.png")
  .async("uint8array", function updateCallback(metadata) {
    console.log("진행률: " + metadata.percent.toFixed(2) + " %");
  })
  .then(function (u8) {
    // 결과 처리
  });
```

## Other examples

```js
zip
  .file("my_text.txt")
  .async("string")
  .then(
    function success(content) {
      // content 사용
    },
    function error(e) {
      // 오류 처리
    }
  );
```
