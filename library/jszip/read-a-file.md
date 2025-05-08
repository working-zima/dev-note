# How to read a file

이 페이지에서는 **기존 zip 파일을 읽는 방법**과 **기존 파일을 zip 파일에 추가하는 방법**을 설명합니다.

## 브라우저에서

### AJAX 요청으로 zip 파일 읽기

AJAX로 바이너리 데이터를 가져오는 것은 (특히 IE 9 이하에서) 까다롭습니다.\
가장 쉬운 방법은 `JSZipUtils.getBinaryContent`를 사용하는 것입니다.

#### 콜백 방식 예시

```js
JSZipUtils.getBinaryContent("path/to/content.zip", function (err, data) {
  if (err) {
    throw err; // 또는 에러 처리
  }

  JSZip.loadAsync(data).then(function () {
    // zip 파일 읽기 성공
  });
});
```

#### Promise 방식 예시

```js
new JSZip.external.Promise(function (resolve, reject) {
    JSZipUtils.getBinaryContent('path/to/content.zip', function(err, data) {
        if (err) {
            reject(err);
        } else {
            resolve(data);
        }
    });
})
.then(function (data) {
    return JSZip.loadAsync(data);
})
.then(...);
```

#### 참고: getBinaryContent 없이 직접 AJAX로 할 경우

기본 `XMLHttpRequest`에서 `responseType`을 지정하지 않으면 브라우저가 응답을 문자열로 해석하고 문자셋 디코딩을 시도합니다.\
이것을 피하려면 다음과 같이 처리합니다:

- Firefox / Chrome / Opera:  
  `xhr.overrideMimeType("text/plain; charset=x-user-defined");`
- IE 9 이하는 비표준 방식(vbscript 등) 필요
- IE 10 이상은 `xhr.responseType = "arraybuffer"` 사용 가능

### 로컬 파일 읽기 (FileReader API)

브라우저가 `FileReader` API를 지원하면, zip 파일을 로컬에서 직접 읽을 수 있습니다.\
JSZip은 `ArrayBuffer`를 읽을 수 있기 때문에 다음과 같이 사용합니다:

```js
const reader = new FileReader();
reader.onload = function (e) {
  JSZip.loadAsync(e.target.result).then(function (zip) {
    // zip 처리
  });
};
reader.readAsArrayBuffer(file);
```

## Node.js에서

JSZip은 `Buffer`를 읽을 수 있기 때문에 매우 간단하게 사용 가능합니다.

### 로컬 zip 파일 읽기

```js
const fs = require("fs");
const JSZip = require("jszip");

fs.readFile("test.zip", function (err, data) {
  if (err) throw err;
  JSZip.loadAsync(data).then(function (zip) {
    // zip 처리
  });
});
```

또는 Promise 방식:

```js
new JSZip.external.Promise(function (resolve, reject) {
    fs.readFile("test.zip", function(err, data) {
        if (err) {
            reject(err);
        } else {
            resolve(data);
        }
    });
})
.then(JSZip.loadAsync)
.then(...);
```

### 기존 파일을 zip에 추가하기

```js
fs.readFile("picture.png", function (err, data) {
  if (err) throw err;
  const zip = new JSZip();
  zip.file("picture.png", data);
});
```

Promise 방식:

```js
const contentPromise = new JSZip.external.Promise(function (resolve, reject) {
  fs.readFile("picture.png", function (err, data) {
    if (err) {
      reject(err);
    } else {
      resolve(data);
    }
  });
});
zip.file("picture.png", contentPromise);
```

### 파일 스트림으로 추가하기

```js
const stream = fs.createReadStream("picture.png");
zip.file("picture.png", stream);
```

## 원격 파일 읽기

Node.js에서는 다양한 HTTP 라이브러리를 사용할 수 있습니다.\
(zip 파일은 가능한 한 **Buffer**로 다운로드하는 것이 성능에 좋습니다.)

### http 모듈 사용

```js
const http = require("http");
const url = require("url");
const JSZip = require("jszip");

const req = http.get(
  url.parse("http://localhost/.../file.zip"),
  function (res) {
    if (res.statusCode !== 200) {
      console.log(res.statusCode);
      return;
    }

    const data = [];
    let dataLen = 0;

    res.on("data", function (chunk) {
      data.push(chunk);
      dataLen += chunk.length;
    });

    res.on("end", function () {
      const buf = Buffer.concat(data);
      JSZip.loadAsync(buf)
        .then(function (zip) {
          return zip.file("content.txt").async("string");
        })
        .then(console.log);
    });
  }
);

req.on("error", function (err) {
  // 에러 처리
});
```

### request 모듈 사용

```js
const request = require("request");
const JSZip = require("jszip");

request(
  {
    method: "GET",
    url: "http://localhost/.../file.zip",
    encoding: null, // 중요! 바이너리로 받기
  },
  function (error, response, body) {
    if (error || response.statusCode !== 200) {
      // 에러 처리
      return;
    }

    JSZip.loadAsync(body)
      .then(function (zip) {
        return zip.file("content.txt").async("string");
      })
      .then(console.log);
  }
);
```
