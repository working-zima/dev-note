# ESLint

🚀 [**ESLint 공식문서**](https://eslint.org/)

- [린트](<https://ko.wikipedia.org/wiki/린트_(소프트웨어)>)
- [정적 프로그램 분석](https://ko.wikipedia.org/wiki/정적_프로그램_분석)
- [Coding_conventions](https://en.wikipedia.org/wiki/Coding_conventions)

## 사용 이유

- 스타일 통일
- 잠재적 문제 발견
- 베스트 프랙티스 추천 → 최신 트렌드를 학습하는데 활용할 수 있다.

## 설치

[VS Code ESLint extension](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

## 세팅

`.vscode/settings.json`파일을 추가해 JS/TS 파일을 저장할 때마다 ESLint를 실행하고 문제점을 고치게 설정할 수 있다.

```json
// 저장하면 자동으로 수정
{
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

아샬이 쓰는 VS Code 기본 세팅:

- [VS Code 기본 세팅](https://github.com/ahastudio/CodingLife/blob/main/20211008/react/.vscode/settings.json)
- [Trailing Spaces](https://marketplace.visualstudio.com/items?itemName=shardulm94.trailing-spaces)

```bash
npm run lint && npm run check
```

## Parcel 실행시 오류 상황 발생

어느날 Parcel을 실행하였는데, 아래와 같은 메시지와 함께 실행이 되지 않았습니다.

```bash
libc++abi: terminating with uncaught exception of type std::__1::system_error: mutex lock failed: Invalid argument [1] 8265 abort npm start
```

### 해결

프로젝트 폴더에서 .cache와 .parcel-cache 디렉토리를 삭제하고, 빌드 후 생성되는 임시 파일 제거.

```bash
rm -rf .cache
rm -rf .parcel-cache
```

현재 프로젝트 폴더에서 node_modules 폴더를 삭제하고, 재설치.

```bash
rm -rf node_modules
npm install
```