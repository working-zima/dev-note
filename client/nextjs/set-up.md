# Project Setup

## 수동 설치

설치하기

```bash
npm i react@latest next@latest react-dom@latests
```

package 스크립트 추가

```json
// package.json

"scripts": {
  "dev": "next dev"
},
```

app 폴더 생성 & page.tsx 파일 생성

```bash
mkdir -p app && touch app/page.tsx
```

실행하여 `layout.tsx` 생성

```bash
npm run dev
```
