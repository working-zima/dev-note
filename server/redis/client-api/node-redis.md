# node-redis 가이드 (JavaScript)

## Node.js/JavaScript 애플리케이션에서 Redis 데이터베이스에 연결하기

`node-redis`는 Node.js/JavaScript용 Redis 클라이언트입니다.\
아래 섹션에서는 `node-redis` 설치 방법과 애플리케이션을 Redis 데이터베이스에 연결하는 방법을 설명합니다.

node-redis를 사용하려면 실행 중인 Redis 또는 Redis Stack 서버가 필요합니다.\
설치 방법은 Getting started를 참조하세요.

객체 매핑 인터페이스
Redis를 객체 매핑 클라이언트 인터페이스를 통해 사용할 수도 있습니다. 자세한 내용은 RedisOM for Node.js를 참조하세요.

## 설치

다음 명령어로 `node-redis`를 설치하세요.

```bash
npm install redis
```

## 연결 및 테스트

### 로컬 Redis에 연결

localhost에서 포트 6379로 연결하는 예제입니다.

```jsx
import { createClient } from "redis";

// Redis 클라이언트 생성
const client = createClient();

// 에러 이벤트 리스너
client.on("error", (err) => console.log("Redis Client Error", err));

// Redis 연결
await client.connect();
```

### 문자열 저장 및 조회

Redis에 간단한 문자열을 저장하고 조회하는 방법입니다.

```jsx
// 문자열 저장
await client.set("key", "value");

// 문자열 조회
const value = await client.get("key");
console.log(value); // 'value'
```

### 해시(Hash) 저장 및 조회

해시 자료형을 저장하고 조회하는 방법입니다.

```jsx
코드 복사
// 해시 데이터 저장
await client.hSet('user-session:123', {
name: 'John',
surname: 'Smith',
company: 'Redis',
age: 29
});

// 해시 데이터 조회
const userSession = await client.hGetAll('user-session:123');
console.log(JSON.stringify(userSession, null, 2));
/*
{
  "surname": "Smith",
  "name": "John",
  "company": "Redis",
  "age": "29"
}
*/
```

## 다른 호스트나 포트에 연결

다른 호스트나 포트로 연결하려면 아래와 같은 URL 형식을 사용하세요:

```jsx
const client = createClient({
  url: "redis://alice:foobared@awesome.redis.server:6380",
});

await client.connect();
```

## 클라이언트 상태 확인

- `client.isReady`: 클라이언트가 명령을 보낼 준비가 되었는지 여부를 반환합니다 (`true`/`false`).
- `client.isOpen`: 클라이언트 소켓이 열려 있는지 여부를 반환합니다 (`true`/`false`).

```jsx
if (client.isReady) {
  console.log("Redis 클라이언트가 준비되었습니다.");
}
```

## Redis 클러스터에 연결

Redis 클러스터에 연결하려면 `createCluster`를 사용합니다.

```jsx
import { createCluster } from "redis";

// 클러스터 생성
const cluster = createCluster({
  rootNodes: [
    { url: "redis://127.0.0.1:16379" },
    { url: "redis://127.0.0.1:16380" },
  ],
});

// 에러 리스너
cluster.on("error", (err) => console.log("Redis Cluster Error", err));

// 클러스터 연결
await cluster.connect();

// 데이터 저장 및 조회
await cluster.set("foo", "bar");
const value = await cluster.get("foo");
console.log(value); // 'bar'

// 클러스터 연결 종료
await cluster.quit();
```

## 프로덕션 Redis에 TLS로 연결

프로덕션 환경에서는 TLS를 사용하고 Redis 보안 가이드를 준수하세요.

```jsx
import { readFileSync } from "fs";

const client = createClient({
  username: "default",
  password: "secret", // 실제 비밀번호를 사용하세요
  socket: {
    host: "my-redis.cloud.redislabs.com",
    port: 6379,
    tls: true,
    key: readFileSync("./redis_user_private.key"),
    cert: readFileSync("./redis_user.crt"),
    ca: [readFileSync("./redis_ca.pem")],
  },
});

// 에러 리스너
client.on("error", (err) => console.log("Redis Client Error", err));

// 연결
await client.connect();

// 데이터 저장 및 조회
await client.set("foo", "bar");
const value = await client.get("foo");
console.log(value); // 'bar'

// 연결 해제
await client.disconnect();
```

## 에러 처리

node-redis는 여러 이벤트를 통해 에러를 처리합니다.\
특히 error 이벤트는 반드시 리스너로 처리해야 합니다.

```jsx
const client = createClient({
  // 클라이언트 옵션
});

// 에러 리스너
client.on("error", (error) => {
  console.error("Redis 클라이언트 에러:", error);
});
```

## 재연결 처리

Redis 연결이 끊겼을 때 재연결 전략을 설정할 수 있습니다.

```jsx
const client = createClient({
  socket: {
    reconnectStrategy: (retries) => {
      if (retries > 20) {
        console.log("재연결 시도가 너무 많습니다. 연결이 종료됩니다.");
        return new Error("Too many retries.");
      }
      return retries \* 500; // 재연결 지연 시간(ms)
    }
  }
});

client.on('error', (error) => console.error('Redis 클라이언트 에러:', error));
```

## 연결 타임아웃 설정

타임아웃은 `connectTimeout` 옵션으로 설정할 수 있습니다.

```jsx
const client = createClient({
  socket: {
    connectTimeout: 10000, // 10초
  },
});

client.on("error", (error) => console.error("Redis 클라이언트 에러:", error));
```
