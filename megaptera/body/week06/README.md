# 테스트

## 6주차 학습 목표

관심사의 분리에 따라 Store 를 사용해 상태관리를 할 수 있습니다.

## 목차

- [External Store](./external-store.md)
- [TSyringe](./react-testing-library.md)
- [Redux 따라하기](./redux.md)
- [usestore-ts](./usestore-ts.md)

## 주간 회고

### 배운 점

하나의 어플리케이션을 Presentation, Business, Persistence layer로 나누어 설계하며 관심사 분리를 통해 하나의 역할만 가진 코드로 분리합니다.\
또한 Input → Process → Output으로 순환하는 구조로 구분하여 코드를 이해하고 유지보수하는데 도움을 받을 수 있습니다.

위와 같이 관심사 분리를 하기 위해 외부에서 External Store 라는 형태로 상태를 관리하여 React가 UI를 담당하고, 순수한 TypeScript(또는 JavaScript)가 비즈니스 로직을 담당하도록 할 수 있습니다.

이번 강의들에서는 외부에서 상태를 관리 할 수 있도록 도와주는 방법들을 구현해보았습니다.

### 고민해야할 점

강의에 맞게 코드를 구현해봤지만 완전히 이해했다고 생각하지 않습니다.
물론 해당 코드를 라이브러리로 구현한 코드들을 사용하는 것이 일반적이기 때문에 완벽히 암기할 할 필요는 없어보이지만 내부적으로 어떻게 작동하는 것인지에 대한 이해는 필요해보입니다.
