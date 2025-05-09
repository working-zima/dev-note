# nodeStream(type[, onUpdate])

지정한 형식으로 파일 내용을 [Node.js Streams3](https://github.com/nodejs/readable-stream) 형태로 반환합니다.\
파일을 직접 디스크에 스트리밍하거나, 파이프 처리할 때 사용할 수 있습니다.

**Returns** : [Node.js Streams3](https://github.com/nodejs/readable-stream) 객체

## Arguments

| name     | type     | default      | description                                |
| -------- | -------- | ------------ | ------------------------------------------ |
| type     | string   | `nodebuffer` | 현재는 `nodebuffer`만 지원됩니다           |
| onUpdate | function |              | 처리 진행 중마다 호출되는 선택적 콜백 함수 |

**Metadata** : 진행률 등 메타데이터는 [`async()` 문서의 onUpdate 콜백 설명]({{site.baseurl}}/documentation/api_zipobject/async.html#onupdate-callback)를 참고하세요.

--

## Example

```js
zip
  .file("my_text.txt")
  .nodeStream()
  .pipe(fs.createWriteStream("/tmp/my_text.txt"))
  .on("finish", function () {
    // JSZip은 "end" 이벤트를 갖는 읽기 스트림을 생성하지만,
    // pipe 대상인 쓰기 스트림은 "finish" 이벤트를 사용함
    console.log("text file written.");
  });
```
