# 온라인 쇼핑몰 추가 기능 개발

## 목차

- [개발하기 전 준비](./before-cording.md)
- [로그인](./log-in.md)
- [로그아웃](./log-out.md)
- [회원가입](./sign-up.md)
- [주문 목록 & 주문 상세](./order-list-and-details.md)

## 주간 회고

### 배운 점

`npm`으로 패키지를 설치할 때 `npm ERR! could not detect node name from path or package`라는 오류가 발생했습니다.\
이상하게 main 브랜치는 정상인데 제 브랜치에만 위와 같은 오류가 발생했습니다.\
이후 다음과 같은 방법을 시도해 봤습니다.

1. main 브랜치와 `package.json`, `package-lock.json` 비교
2. npm 무결성 확인(`npm cache verify`)
3. 프로젝트를 삭제하고 재 clone
4. 다른 프로젝트에서 npm 확인
5. npm 글로벌 설치(`npm i npm -g`)

위의 방법을 시도해 봤지만 모두 해결법은 아니였습니다.\
해결법은 `package-lock.json`을 삭제하고 다시 install하는 것이였습니다.

### 고민해야할 점

프로젝트를 수업과 동일하게 진행하지만 동일하지 않은 결과가 발생하는 일이 잦았습니다.\
이럴 때 혼자 해결하려고 시간을 많이 소모했는데 해결하지 못해 도움을 받게 되었습니다.\
혼자 해결했을 때 기쁨을 느끼고 싶은데 그렇지 못해서 아쉬움이 많습니다.\
열심히 공부합시다.
