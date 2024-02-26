# 1. Express

## 학습 키워드

- Express 란
- URL 구조
- REST API
- HTTP Method(CRUD)

## Express

Node.js의 서버 웹 프레임워크입니다.

### 간단한 서버 앱 npm 패키지 세팅

```bash
mkdir express-demo-app
cd express-demo-app
```

잊지 말고 .gitignore 파일 준비

```bash
touch .gitignore
echo "/node_modules/" > .gitignore
```

패키지 초기화

```bash
npm init -y
```

TypeScript

```bash
npm i -D typescript
npx tsc --init
```

ts-node 설치\
(TypeScript 코드를 실행하는 데 사용되는 도구로, 기본적으로 TypeScript를 컴파일하고 실행하는 데 필요한 단계를 하나로 통합합니다.)

```bash
npm i -D ts-node
```

ESLint 설치

TypeScript를 사용하기 때문에 `What type of modules does your project use?` 항목에서 `JavaScript modules (import/export)` 선택

```bash
npm i -D eslint
npx eslint --init
```

Express 설치

```bash
npm i express
npm i -D @types/express
```

### Hello World 예제

TypeScript에 맞춰서 `app.ts` 파일 작성.

```bash
touch app.js
```

```typescript
import express from 'express';

const port = 3000;

const app = express();

app.get('/', (req, res) => {
  res.send('Hello, world!');
});

app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

ts-node로 실행.

```bash
npx ts-node app.ts
```

코드를 수정할 때마다 서버를 재실행해야 하는 문제를 피하기 위해 [nodemon](https://github.com/remy/nodemon) 사용합니다.

```bash
npm i -D nodemon
npx nodemon app.ts
```

## REST API

> Roy Fielding - “[Architectural Styles and the Design of Network-based Software Architectures](https://ics.uci.edu/~fielding/pubs/dissertation/top.htm)” (2000)
>
> [Richardson Maturity Model](https://martinfowler.com/articles/richardsonMaturityModel.html)

대개는 “필딩 제약 조건” 4가지를 모두 만족하지 않고, Resource와 HTTP Verb만 도입하는 수준으로 사용합니다.
API를 만들 때, 각각의 데이터를 리소스로 표현하고, 그에 따라 HTTP 동사(GET, POST, PUT, DELETE 등)를 사용하여 해당 데이터를 다루는 방식을 의미합니다.

- `/write-post` 처럼 안 하고 `/posts → 뭔가를 한다 (CRUD)`로 함

CRUD에 대해 HTTP Method를 대입. Read는 Collection(복수)과 Item(Element)(단수)로 나뉨.

기본 리소스 URL: /products

1. Read (Collection) → GET /products ⇒ 상품 목록(복수) 확인
2. Read (Item) → GET /products/{id} ⇒ 특정 상품(단수) 정보 확인
3. Create (Collection Pattern 활용) → POST /products ⇒ 상품 추가 (JSON 정보 함께 전달)
4. Update (Item) → PUT(덮어쓰기) 또는 PATCH(일부만) /products/{id} ⇒ 특정 상품 정보 변경 (JSON 정보 함께 전달)
5. Delete (Item) → DELETE /products/{id} ⇒ 특정 상품 삭제

### Thinking in React 예제

```javascript
app.get('/products', (req, res) => {
  const products = [
    {
      category: 'Fruits',
      price: '$1',
      stocked: true,
      name: 'Apple',
    },
    {
      category: 'Fruits',
      price: '$1',
      stocked: true,
      name: 'Dragonfruit',
    },
    {
      category: 'Fruits',
      price: '$2',
      stocked: false,
      name: 'Passionfruit',
    },
    {
      category: 'Vegetables',
      price: '$2',
      stocked: true,
      name: 'Spinach',
    },
    {
      category: 'Vegetables',
      price: '$4',
      stocked: false,
      name: 'Pumpkin',
    },
    {
      category: 'Vegetables',
      price: '$1',
      stocked: true,
      name: 'Peas',
    },
  ];
  res.send({ products });
});
```

## 참고 자료

[Express](https://expressjs.com/ko/)\
[Express 설치](https://expressjs.com/ko/starter/installing.html)\
[ts-node](https://github.com/TypeStrong/ts-node)\
[Express 예제](https://expressjs.com/ko/starter/hello-world.html)
