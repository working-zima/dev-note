# generateNodeStream(options[, onUpdate])

Node.js 환경에서 zip 파일을 **스트림 형태로 생성**합니다.  
대용량 파일 생성 시 메모리 사용을 최소화할 수 있습니다.

**Returns** : [Node.js Streams3](https://github.com/nodejs/readable-stream) 객체를 반환합니다.

**Since**: v3.0.0

## Arguments

| name     | type     | default     | description                                                                                                              |
| -------- | -------- | ----------- | ------------------------------------------------------------------------------------------------------------------------ |
| options  | object   |             | zip 파일 생성을 위한 설정. [`generateAsync()` 옵션]({{site.baseurl}}/documentation/api_jszip/generate_async.html)과 동일 |
| onUpdate | function | (선택 사항) | 내부 처리 진행 시마다 호출되는 콜백 함수                                                                                 |

`type` 옵션은 기본값이 `nodebuffer`이며,  
현재 `nodebuffer`만 지원됩니다.

메타데이터 정보는 [`generateAsync()`의 onUpdate 콜백]({{site.baseurl}}/documentation/api_jszip/generate_async.html#onupdate-callback) 설명을 참고하세요.

## Examples

```js
zip
  .generateNodeStream({ streamFiles: true })
  .pipe(fs.createWriteStream("out.zip"))
  .on("finish", function () {
    // JSZip은 "end" 이벤트를 가지는 읽기 스트림을 생성하지만,
    // pipe된 대상이 쓰기 스트림이므로 "finish" 이벤트를 사용합니다.
    console.log("out.zip written.");
  });
```
