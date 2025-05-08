# How To use JSZip

JSZip의 인스턴스는 파일들의 집합을 나타냅니다.\
파일을 추가하거나, 제거하거나, 수정할 수 있습니다.\
또한 기존 zip 파일을 가져오거나 새로운 zip 파일을 생성할 수도 있습니다.

## 객체 가져오기

### 브라우저에서 사용 시

브라우저에서 사용하려면 두 가지 주요 파일이 있습니다:\
`dist/jszip.js` 와 `dist/jszip.min.js` (둘 중 하나만 포함하면 됩니다).

- **AMD 로더(예: RequireJS)** 사용 시\
  JSZip은 자동으로 등록됩니다. JS 파일을 적절한 위치에 두거나 로더 설정을 해주면 됩니다.\
  (관련 문서: [RequireJS에서 사용하기](https://stuk.github.io/jszip/documentation/howto/read_zip.html))

- **로더 없이 사용 시**  
  JSZip은 전역 스코프에 `JSZip`이라는 변수를 선언합니다.

### Node.js에서 사용 시

```js
var JSZip = require("jszip");
```

## 기본 조작

먼저 JSZip의 인스턴스를 생성합니다:

```js
var zip = new JSZip();
```

파일과 폴더를 추가하거나 갱신할 수 있습니다:  
(메서드 체이닝 가능)

```js
// 파일 생성
zip.file("hello.txt", "Hello[p my)6cxsw2q");

// 잘못된 입력 수정
zip.file("hello.txt", "Hello World\n");

// 하위 폴더에 파일 생성
zip.file("nested/hello.txt", "Hello World\n");

// 동일한 결과
zip.folder("nested").file("hello.txt", "Hello World\n");
```

`.folder(name)`을 사용하면 해당 폴더에 상대적으로 파일을 추가할 수 있습니다.

```js
var photoZip = zip.folder("photos");

// photos/README 파일 생성
photoZip.file("README", "a folder with photos");
```

## 📄 파일 내용 읽기

```js
zip
  .file("hello.txt")
  .async("string")
  .then(function (data) {
    // data는 "Hello World\n"
  });
```

`Uint8Array`로 읽을 수도 있습니다:

```js
if (JSZip.support.uint8array) {
  zip
    .file("hello.txt")
    .async("uint8array")
    .then(function (data) {
      // data는 Uint8Array { 0=72, 1=101, 2=108, ... }
    });
}
```

## 파일 또는 폴더 제거

```js
zip.remove("photos/README");
zip.remove("photos"); // 폴더를 제거하면 내부 내용도 함께 제거됩니다.
```

## zip 파일 생성하기

```js
var promise = null;

if (JSZip.support.uint8array) {
  promise = zip.generateAsync({ type: "uint8array" });
} else {
  promise = zip.generateAsync({ type: "string" });
}
```

자세한 내용은 [이 문서](https://stuk.github.io/jszip/documentation/howto/write_zip.html)를 참고하세요.

## zip 파일 읽기

```js
var new_zip = new JSZip();

new_zip.loadAsync(content).then(function (zip) {
  // zip 안의 파일 읽기
  zip.file("hello.txt").async("string").then(console.log);
});
```

> zip 읽기는 간단해 보이지만, 자세한 설명은 [이 문서](https://stuk.github.io/jszip/documentation/howto/read_zip.html)를 참고하세요.
