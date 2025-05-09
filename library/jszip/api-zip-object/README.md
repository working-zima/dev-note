# ZipObject API

`ZipObject`는 zip 파일 내의 개별 항목(파일 또는 폴더)을 나타냅니다.\
해당 항목이 [`loadAsync`]({{site.baseurl}}/documentation/api_jszip/load_async.html)로 불러온 기존 zip에서 왔다면, 내용은 자동으로 압축 해제 및 변환됩니다.

## Attributes

| 속성 이름                    | 타입          | 설명                                                                                                              |
| ---------------------------- | ------------- | ----------------------------------------------------------------------------------------------------------------- |
| `name`                       | string        | 항목의 절대 경로 (파일 또는 폴더)                                                                                 |
| `dir`                        | boolean       | 디렉토리이면 true                                                                                                 |
| `date`                       | Date          | 마지막 수정일                                                                                                     |
| `comment`                    | string        | 해당 항목에 대한 주석                                                                                             |
| `unixPermissions`            | 16비트 number | 유닉스 파일 권한 (있을 경우)                                                                                      |
| `dosPermissions`             | 6비트 number  | 도스 파일 권한 (있을 경우)                                                                                        |
| `options`                    | object        | 파일 옵션 객체                                                                                                    |
| `options.compression`        | compression   | 압축 방식 (예: `"STORE"` 또는 `"DEFLATE"`) [자세히 보기]({{site.baseurl}}/documentation/api_jszip/file_data.html) |
| `options.compressionOptions` | object        | 압축 옵션 설정 [자세히 보기]({{site.baseurl}}/documentation/api_jszip/file_data.html)                             |

### Example

```js
{
  name: 'docs/',
  dir: true,
  date: 2016-12-25T08:09:27.153Z,
  comment: null,
  unixPermissions: 16877,
  dosPermissions: null,
  options: {
    compression: 'STORE',
    compressionOptions: null
  }
}
```

```js
{
  name: 'docs/list.txt',
  dir: false,
  date: 2016-12-25T08:09:27.152Z,
  comment: null,
  unixPermissions: 33206,
  dosPermissions: null,
  options: {
    compression: 'DEFLATE',
    compressionOptions: null
  }
}
```
