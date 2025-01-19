# webpack v4

모든 브라우져에서 모듈 시스템을 지원하지는 않습니다.\
브라우져에 무관하게 모듈을 사용하려고 할 때 웹팩을 사용합니다.

## 설치

```bash
npm install --save-dev webpack
# 또는 특정 버전
npm install --save-dev webpack@<version>
```

터미널을 통해 webpack 명령어를 사용할 수 있게 해주는 cli도 설치합니다.

```bash
npm i webpack-cli
```

## 명령어

### 명령어 검색

```bash
node_modules/.bin/webpack --help
```

### 웹팩 실행 모드 지정

개발용 정보를 추가하려면 development, 운영에 최적화 설정을 하려면 production

```bash
node_modules/.bin/webpack --mode development
```

### 모듈의 시작점 경로 지정

```bash
node_modules/.bin/webpack --entry ./src/app.js
```

### 모듈 저장 경로 설정

```bash
node_modules/.bin/webpack --output dist/main.js
```

## index.html

```html
<script src="dist/main.js"></script>
```

## mode, entry, output 설정

웹팩 설정파일

```js
// webpack.config.js

const path = require("path");

module.exports = {
  mode: "development",
  entry: {
    main: "./src/app.js",
  },
  output: {
    path: path.resolve("./dist"), // 절대 경로로 지정
    filename: "[name].js", // entry에서 지정한 파일명으로
  },
};
```

## package.json

`npm run build`로 웹팩 작업을 지시합니다.

```js
// package.json

{
  "scripts": {
    "build": "./node_modules/.bin/webpack"
  }
}
```

또는

```js
// package.json

{
  "scripts": {
    "build": "webpack --progress"
  },
}
```

## 로더

타입스크립트 같은 다른 언어를 자바스크립트 문법으로 변환해 주거나 이미지를 data URL 형식의 문자열로 변환합니다.

### 커스텀 로더

로더를 직접 만들어 사용할 수 있습니다.
아래의 커스텀 로더는 `console.log(` 를 `alert(` 로 치환하는 동작을 수행합니다.

```js
module.exports = function myloader(content) {
  console.log("myloader가 동작함");
  return content.replace("console.log(", "alert(");
};
```

빌드후 확인하면 `console.log()` 함수가 `alert()` 함수로 변경되게 됩니다.

### module 객체를 추가

`test`에는 로딩에 적용할 파일을 지정합니다.\
`use`에는 이 패턴에 해당하는 파일에 적용할 로더를 설정합니다.

```js
// webpack.config.js

const path = require("path");

module.exports = {
  mode: "development",
  entry: {
    main: "./src/app.js",
  },
  output: {
    filename: "[name].js",
    path: path.resolve("./dist"),
  },
  module: {
    rules: [
      {
        test: /\.js$/, // .js 확장자로 끝나는 모든 파일
        use: [path.resolve("./myloader.js")], // 방금 만든 로더를 적용한다
      },
    ],
  },
};
```

### css-loader

CSS 파일을 자바스크립트에서 불러와 사용하려면 CSS를 모듈로 변환하는 작업이 필요합니다.

#### css-loader 설치

```bash
npm install -D css-loader
```

#### css-loader 웹펙에 추가

엔트리 포인트부터 시작해서 모듈을 검색하다가 CSS 파일을 찾으면 css-loader로 처리하게 됩니다.

```js
// webpack.config.js

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/, // .css 확장자로 끝나는 모든 파일
        use: ["css-loader"], // css-loader를 적용한다
      },
    ],
  },
};
```

### style-loader

css-loader로 처리하면 css 코드가 자바스크립트 코드로만 변경되었을 뿐 돔에 적용되지 않습니다.\
때문에 모듈로 변경된 스타일 시트는 style-loader가 동적으로 돔에 추가해야만 브라우저가 해석할 수 있습니다.

#### style-loader 설치

```bash
npm install -D style-loader
```

#### style-loader 웹펙에 추가

```js
// webpack.config.js

module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/, // .css 확장자로 끝나는 모든 파일
        use: ["style-loader", "css-loader"], // style-loader를 앞에 추가
      },
    ],
  },
};
```

### file-loader

파일을 모듈 형태로 지원하고 웹팩 아웃풋에 파일을 옮겨줍니다.

가령 CSS에서 `url()` 함수에 이미지 파일 경로를 지정할 수 있는데 웹팩은 file-loader를 이용해서 이 파일을 처리합니다.

#### file-loader 웹펙에 추가

아래의 설정은 웹팩이 `.png`, `.jpg`, `.gif`, `.svg` 파일을 발견하면 file-loader를 실행합니다.\
로더가 동작하고 나면 아웃풋에 설정한 경로로 이미지 파일을 복사됩니다.

아웃풋에 복사된 이미지 파일은 파일명이 해쉬코드로 변경 됩니다.\
빌드된 결과물에서 파일 내용이 달라도 이름이 같으면 이전 빌드에서 캐싱된 내용을 사용합니다.\
따라서 캐시 무효화를 위해 처리를 위해 파일 이름을 새로운 해쉬값으로 변경합니다.

```js
// webpack.config.js

const path = require("path");

module.exports = {
  mode: "development",
  entry: {
    main: "./src/app.js",
  },
  output: {
    filename: "[name].js",
    path: path.resolve("./dist"), // 아웃풋 경로(절대값)
  },
  module: {
    rules: [
      {
        test: /\.css$/, // .css 확장자로 끝나는 모든 파일
        use: ["style-loader", "css-loader"], // style-loader를 앞에 추가
      },
      {
        test: /\.(png|jpg|gif|svg)$/, // .png, .jpg, .gif, .svg 확장자로 마치는 모든 파일
        loader: "file-loader", // 파일 로더를 적용
        options: {
          publicPath: "./dist/", // prefix를 아웃풋 경로로 지정
          name: "[name].[ext]?[hash]", // 파일명 형식(원본 파일명, 확장자, 생성된 해쉬값)
        },
      },
    ],
  },
};
```

### url-loader

한 페이지에서 사용하는 이미지 갯수가 많다면 네트웍 리소스를 사용하는 부담이 있고 사이트 성능에 영향을 줄 수도 있습니다.\
이미지를 Base64로 인코딩하여 문자열 형태로 소스코드에 넣는 형식인 Data URI Scheme을 이용하는 방법이 더 나은 경우도 있습니다.

#### url-loader 설치

아래의 설정은 5kb 미만인 파일만 url-loader를 적용하는 설정입니다.\
만약 이보다 크면 file-loader가 처리하는데 옵션 중 fallback 기본값이 file-loader이기 때문입니다.

```bash
npm install -D url-loader
```

#### url-loader 웹펙에 추가

```js
// webpack.config.js

const path = require("path");

module.exports = {
  mode: "development",
  entry: {
    main: "./src/app.js",
  },
  output: {
    filename: "[name].js",
    path: path.resolve("./dist"), // 아웃풋 경로(절대값)
  },
  module: {
    rules: [
      {
        test: /\.css$/, // .css 확장자로 끝나는 모든 파일
        use: ["style-loader", "css-loader"], // style-loader를 앞에 추가
      },
      {
        test: /\.(png|jpg|gif|svg)$/, // .png, .jpg, .gif, .svg 확장자로 마치는 모든 파일
        loader: "url-loader",
        options: {
          publicPath: "./dist/", // prefix를 아웃풋 경로로 지정
          name: "[name].[ext]?[hash]", // 파일명 형식(원본 파일명, 확장자, 생성된 해쉬값)
          limit: 5000, // 5kb 미만 파일만 data url로 처리, 5kb 이상은 file-loader 사용
        },
      },
    ],
  },
};
```

## 플러그인

로더가 파일 단위로 처리하는 반면 플러그인은 번들된 결과물을 처리합니다.\
자바스크립트를 난독화 한다거나 특정 텍스트를 추출하는 용도로 사용합니다.

### 커스텀 플러그인

로더와 다르게 플러그인은 클래스로 제작합니다.

```js
// my-webpack-plugin.js

class MyWebpackPlugin {
  // 인자로 받은 webpack compiler 객체 안에 있는 tap() 함수를 사용
  apply(compiler) {
    // 플러그인 작업이 완료되는(done) 시점에 로그를 찍는 코드
    compiler.hooks.done.tap("My Plugin", (stats) => {
      console.log("MyPlugin: done");
    });
  }
}

module.exports = MyPlugin;
```

#### 커스텀 플러그인 웹펙에 추가

```js
// webpack.config.js

const path = require("path");
const MyWebpackPlugin = require("./my-webpack-plugin");

module.exports = {
  mode: "development",
  entry: {
    main: "./src/app.js",
  },
  output: {
    filename: "[name].js",
    path: path.resolve("./dist"),
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ["style-loader", "css-loader"],
      },
      {
        test: /\.(png|jpg|svg|gif)$/,
        loader: "url-loader",
        options: {
          name: "[name].[ext]?[hash]",
          limit: 10000, // 10Kb
        },
      },
    ],
  },
  plugins: [new MyWebpackPlugin()],
};
```

### 웹팩 내장 플러그인 BannerPlugin 코드 예시

`source()`는 번들 소스를 얻어오는 함수입니다.

```js
class MyWebpackPlugin {
  apply(compiler) {
    compiler.hooks.done.tap("My Plugin", (stats) => {
      console.log("MyPlugin: done");
    });

    // compiler.plugin() 함수로 후처리
    // emit 이벤트가 발생하면 실행되면 callback 실행
    compiler.plugin("emit", (compilation, callback) => {
      // source는 웹팩이 번들링한 main.js의 내용
      const source = compilation.assets["main.js"].source();

      // 원본 코드 상단에 banner를 추가
      compilation.assets["main.js"].source = () => {
        const banner = [
          "/**",
          " * 이것은 BannerPlugin이 처리한 결과입니다.",
          " * Build Date: 2019-10-10",
          " */",
        ].join("\n");
        return banner + "\n\n" + source;
      };

      callback();
    });
  }
}
```

빌드된 `main.js`의 상단에 아래와 같은 주석이 추가됩니다.

```js
/**
 * 이것은 BannerPlugin이 처리한 결과입니다.
 * Build Date: 2019-10-10
 */
```

### BannerPlugin

빌드 결과물에 빌드 정보나 커밋 버전같은 걸 추가할 수 있습니다.

```js
// webpack.config.js

const webpack = require("webpack");

module.exports = {
  plugins: [
    new webpack.BannerPlugin({
      banner: "이것은 배너 입니다",
    }),
  ],
};
```

생성자 함수에 전달하는 옵션 객체의 banner 속성에 문자열을 전달할 수 있습니다.

```js
// webpack.config.js

const webpack = require("webpack");

module.exports = {
  plugins: [
    new webpack.BannerPlugin({
      banner: () => `빌드 날짜: ${new Date().toLocaleString()}`,
    }),
  ],
};
```

빌드 날짜 외에서 깃 커밋 해쉬와 빌드한 유저 정보까지 추가할 수 있습니다.

```js
// webpack.config.js

const webpack = require("webpack");
const banner = require("./banner.js");

module.exports = {
  plugins: [
    new webpack.BannerPlugin(banner);
  ],
};
```

```js
// ./banner.js

// child_process는 Node.js에서 외부 명령 실행을 도와주는 내장 모듈,
// 쉘 명령어(git, ls, npm) 등 다양한 시스템 명령을 실행할 수 있음
const childProcess = require("child_process");

module.exports = function banner() {
  // execSync는 동기적으로 명령어를 실행하고, 명령 실행 결과를 Buffer 형식 문자열로 반환
  const commit = childProcess.execSync("git rev-parse --short HEAD");
  const user = childProcess.execSync("git config user.name");
  const date = new Date().toLocaleString();

  return (
    `commitVersion: ${commit}` + `Build Date: ${date}\n` + `Author: ${user}`
  );
};
```

### DefinePlugin

어플리케이션은 개발환경과 운영환경으로 나눠서 운영합니다.\
같은 소스 코드를 두 환경에 배포하기 위해서는 이러한 환경 의존적인 정보를 소스가 아닌 곳에서 관리하는 것이 좋습니다.\
웹팩은 이런 환경 정보를 제공하기 위해 DefinePlugin을 제공합니다.

```js
// webpack.config.js

const webpack = require("webpack");
const path = require("path");
const childProcess = require("child_process");

module.exports = {
  mode: "development",
  entry: {
    main: "./src/app.js",
  },
  output: {
    filename: "[name].js",
    path: path.resolve("./dist"),
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ["style-loader", "css-loader"],
      },
      {
        test: /\.(png|jpg|svg|gif)$/,
        loader: "url-loader",
        options: {
          name: "[name].[ext]?[hash]",
          limit: 10000, // 10Kb
        },
      },
    ],
  },
  plugins: [
    new webpack.BannerPlugin({
      banner: `
        Build Date: ${new Date().toLocaleString()}
        Commit Version: ${childProcess.execSync("git rev-parse --short HEAD")}
        Author: ${childProcess.execSync("git config user.name")}
      `,
    }),
    new webpack.DefinePlugin({}),
  ],
};
```

`mode`가 `development`이기 때문에 development가 콘솔에 찍힙니다.

```js
app.js;

console.log(process.env.NODE_ENV); // "development"
```

코드가 아닌 값을 입력하려면 문자열화 한 뒤 넘깁니다.

```js
new webpack.DefinePlugin({
  VERSION: JSON.stringify("v.1.2.3"),
  PRODUCTION: JSON.stringify(false),
  MAX_COUNT: JSON.stringify(999),
  "api.domain": JSON.stringify("http://dev.api.domain.com"),
});
```

```js
console.log(VERSION); // 'v.1.2.3'
console.log(PRODUCTION); // true
console.log(MAX_COUNT); // 999
console.log(api.domain); // 'http://dev.api.domain.com'
```

### HtmlWebpackPlugin

웹팩의 기본 플러그인이 아닌 써드 파티 패키지입니다.\
빌드 타임의 값을 넣거나 코드를 압축하는 HTML 파일을 후처리하는데 사용합니다.

#### HtmlWebpackPlugin 설치

```bash
npm install -D html-webpack-plugin
```

#### HtmlWebpackPlugin 플러그인 웹펙에 추가

```js
// webpack.config.js

const path = require("path");
const webpack = require("webpack");
const childProcess = require("child_process");
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  mode: "development",
  entry: {
    main: "./src/app.js",
  },
  output: {
    filename: "[name].js",
    path: path.resolve("./dist"),
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ["style-loader", "css-loader"],
      },
      {
        test: /\.(png|jpg|svg|gif)$/,
        loader: "url-loader",
        options: {
          name: "[name].[ext]?[hash]",
          limit: 10000, // 10Kb
        },
      },
    ],
  },
  plugins: [
    new webpack.BannerPlugin({
      banner: `
        Build Date: ${new Date().toLocaleString()}
        Commit Version: ${childProcess.execSync("git rev-parse --short HEAD")}
        Author: ${childProcess.execSync("git config user.name")}
      `,
    }),
    new webpack.DefinePlugin({}),
    new HtmlWebpackPlugin({
      template: "./src/index.html", // 템플릿 경로를 지정
      templateParameters: {
        // 템플릿에 주입할 파라매터 변수 지정
        env: process.env.NODE_ENV === "development" ? "(개발용)" : "",
      },
      minify:
        process.env.NODE_ENV === "production"
          ? {
              collapseWhitespace: true, // 빈칸 제거
              removeComments: true, // 주석 제거
            }
          : false,
    }),
  ],
};
```

`index.html`을 `src` 폴더 내부에 포함하여 동적으로 HTML 코드를 생성합니다.\
`title`을 동적으로 변경하고 `production` 환경일 때 주석 및 빈칸을 제거합니다.\
또한 `output`로 지정된 경로와 이름으로 `script`가 생성됩니다.

```html
<!-- src/index.html -->

<!DOCTYPE html>
<html>
  <head>
    <!-- ejs 문법을 이용하여 development일 때 (개발용)이 들어감 -->
    <title>타이틀<%= env %></title>
  </head>
  <body>
    <!-- 로딩 스크립트 제거해도 -->
    <!-- <script src="dist/main.js"></script>가 생성됨 -->
  </body>
</html>
```

### CleanWebpackPlugin

빌드 이전 결과물을 제거하는 플러그인입니다.

#### CleanWebpackPlugin 설치

```bash
npm install -D clean-webpack-plugin
```

#### CleanWebpackPlugin 플러그인 웹펙에 추가

```js
// webpack.config.js

const { CleanWebpackPlugin } = require("clean-webpack-plugin");

module.exports = {
  plugins: [new CleanWebpackPlugin()],
};
```

### MiniCssExtractPlugin

MiniCssExtractPlugin은 CSS를 별로 파일로 뽑아내는 플러그인입니다.

브라우져에서 큰 파일 하나를 내려받는 것 보다, 여러 개의 작은 파일을 동시에 다운로드하는 것이 더 빠릅니다.\
따라서 스타일시트가 점점 많아지면 스타일시트 코드만 뽑아서 별도의 CSS 파일로 만들어 역할에 따라 파일을 분리하는 것이 좋습니다.

#### MiniCssExtractPlugin 설치

```bash
npm install -D mini-css-extract-plugin
```

#### MiniCssExtractPlugin 플러그인 웹펙에 추가

`production` 환경일 경우만 이 플러그인을 추가합니다.\
`filename`에 설정한 값으로 아웃풋 경로에 CSS 파일이 생성합니다.

`development` 환경에서는 `css-loader`에 의해 자바스크립트 모듈로 변경된 스타일시트를 적용하기위해 `style-loader`를 사용합니다.\
`production` 환경에서는 별도의 CSS 파일으로 추출하는 플러그인을 적용했으므로 플러그인에서 제공하는 `MiniCssExtractPlugin.loader` 로더를 사용합니다.

```js
// webpack.config.js

const path = require("path");
const webpack = require("webpack");
const childProcess = require("child_process");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const { CleanWebpackPlugin } = require("clean-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  mode: "development",
  entry: {
    main: "./src/app.js",
  },
  output: {
    filename: "[name].js",
    path: path.resolve("./dist"),
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          process.env.NODE_ENV === "production"
            ? MiniCssExtractPlugin.loader // 프로덕션 환경
            : "style-loader", // 개발 환경
          "css-loader",
        ],
      },
      {
        test: /\.(png|jpg|svg|gif)$/,
        loader: "url-loader",
        options: {
          name: "[name].[ext]?[hash]",
          limit: 10000,
        },
      },
    ],
  },
  plugins: [
    new webpack.BannerPlugin({
      banner: `
        Build Date: ${new Date().toLocaleString()}
        Commit Version: ${childProcess.execSync("git rev-parse --short HEAD")}
        Author: ${childProcess.execSync("git config user.name")}
      `,
    }),
    new webpack.DefinePlugin({}),
    new HtmlWebpackPlugin({
      template: "./src/index.html",
      templateParameters: {
        env: process.env.NODE_ENV === "development" ? "(개발용)" : "",
      },
      minify:
        process.env.NODE_ENV === "production"
          ? {
              collapseWhitespace: true,
              removeComments: true,
            }
          : false,
    }),
    new CleanWebpackPlugin(),
    ...(process.env.NODE_ENV === "production"
      ? [new MiniCssExtractPlugin({ filename: `[name].css` })]
      : []),
  ],
};
```

## 참고

[프론트엔드 개발환경의 이해: 웹팩(기본)](https://jeonghwan-kim.github.io/series/2019/12/10/frontend-dev-env-webpack-basic.html#4-%EC%9E%90%EC%A3%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EB%A1%9C%EB%8D%94)
