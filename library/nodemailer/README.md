---
description: Node.js 애플리케이션에서 이메일을 쉽게 보낼 수 있도록 도와주는 모듈인 Nodemailer에 관한 내용입니다.
---

# Nodemailer

## 설치

```bash
npm install nodemailer
```

## 주요 특징

- 단일 모듈, 종속성 없음: 코드 감사가 쉬움.
- 보안 강화: RCE 취약점 방지.
- 유니코드 지원: 이모지 포함 모든 문자 사용 가능.
- Windows 지원: 컴파일 종속성 없이 설치 가능.
- HTML 콘텐츠 전송: Plain Text 대체 콘텐츠 포함 가능.
- 첨부 파일: 이메일에 파일 첨부 가능.
- 이미지 삽입: HTML 이메일에 이미지 삽입 가능.
- TLS/STARTTLS 지원: 안전한 이메일 전송.
- 다양한 전송 방법: 기본 SMTP 외에도 다양한 전송 방법 제공.
- DKIM 서명: 이메일 서명 가능.
- OAuth2 인증 지원.
- SMTP 연결을 위한 프록시 지원.
- ES6 코드 기반: var의 메모리 누수 위험 감소.
- 테스트 이메일 계정 생성: Ethereal.email과 연동.

## 요구 사항

- Node.js 버전 6.0.0 이상 필요.
- 모든 Nodemailer 메서드는 콜백과 Promise를 지원.
- `async/await`를 사용하려면 Node.js 버전 8.0.0 이상 필요.

## Nodemailer 사용 방법 요약

1. Transporter 생성: SMTP 또는 다른 전송 방법 설정.
2. 메시지 옵션 설정: 발신자, 수신자, 내용 등 설정.
3. 이메일 전송: `sendMail()` 메서드 사용.

## 예제 코드

아래는 Ethereal Email을 이용해 Plain Text와 HTML 콘텐츠가 포함된 이메일을 보내는 예제입니다.

```jsx
const nodemailer = require("nodemailer");

// Transporter 설정
const transporter = nodemailer.createTransport({
  host: "smtp.ethereal.email",
  port: 587,
  secure: false, // true for port 465, false for other ports
  auth: {
    user: "maddison53@ethereal.email", // Ethereal 이메일 계정
    pass: "jn7jnAPss4f63QBp6D", // Ethereal 비밀번호
  },
});

// 비동기 함수로 이메일 전송
async function main() {
  // 이메일 전송
  const info = await transporter.sendMail({
    from: '"Maddison Foo Koch 👻" <maddison53@ethereal.email>', // 발신자
    to: "bar@example.com, baz@example.com", // 수신자
    subject: "Hello ✔", // 제목
    text: "Hello world?", // 텍스트 본문
    html: "<b>Hello world?</b>", // HTML 본문
  });

  console.log("Message sent: %s", info.messageId);
  // 출력: Message sent: <유니크 메시지 ID>
}

// 함수 실행
main().catch(console.error);
```
