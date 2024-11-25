# TDD

TDD는 코드 작성 전에 테스트를 작성하고 테스트에 통과하도록 코드를 작성하는 것입니다.

## TDD 순서

흔히 레드-그린 테스트라고 하는데요.\
보통 테스트를 작성하기 전에 약간의 코드를 작성해서 테스트에 오류가 발생하지 않도록 합니다.\
그리고 테스트에 실패하는 레드 테스트를 먼저 실행하고 코드를 모두 작성한 후에는 통과하는 테스트로 그린 테스트를 확인하는 것입니다.

1. 아무것도 하지 않는 빈 함수를 작성합니다
2. 테스트를 작성합니다.
3. 함수가 아무것도 하지 않기 때문에 테스트에 실패합니다.
4. 함수에 코드를 작성합니다.
5. 테스트에 통과합니다.

## TDD 사용 이유

1. 변경 사항 확인을 위해 애플리케이션을 열고 수동으로 테스트할 필요가 없습니다.

2. 개발하면서 모든 테스트를 작성해 두면 변경 사항이 생길 때마다 모든 테스트를 다시 실행해서 '자동(Free)' 회귀 테스트를 할 수 있습니다.

## Test 종류

### Unit(유닛) Tests

입력을 가져가고 출력을 반환하는 하나의 애플리케이션을 분리된 단위로 테스트합니다.

입력 X 및 Y에 대해 출력 Z를 예상할 수 있는 구조가 명확한 함수가 있을 때 사용합니다.

애플리케이션의 모든 단위를 테스트해서 잘 작동하면 전체 애플리케이션도 작동하리라 추측할 수 있기 때문에 보통 가장 많이 사용하는 테스트입니다.

보통 함수나 별개의 React 컴포넌트 코드의 한 Unit 혹은 단위를 테스트하는 겁니다.\
Unit 다른 코드의 유닛과 상호 작용하는 것을 테스트하지 않습니다.\
Unit 테스트는 테스트를 최대한 격리시키고 함수나 컴포넌트를 테스트할 때 의존성을 표시합니다.

장점으로는 테스트가 코드의 한 Unit에 격리되어 있기 때문에 테스트가 실패하면 어디를 확인해야 하는지 정확하게 알 수 있습니다.

단점으로는 사용자가 소프트웨어와 상호 작용하는 방식과는 거리가 멀기 때문에 소프트웨어와 상호 작용하는 사용자가 실패하더라도 테스트를 통과할 수 있고 그 반대로 사용자가 소프트웨어와 상호 작용하는데 문제가 없어도 테스트에 실패할 수 있습니다.\
또한 리팩토링(Refactoring)으로 동작을 변경하지 않고 소프트웨어 작성 방식을 변경하는 것만으로 테스트에 실패할 수 있습니다.\
따라서 소프트웨어가 제대로 작동하면 테스트도 통과해야 하기 때문에 이런 점은 단점이 됩니다.

### Integration(통합) Tests

여러 유닛이 함께 작동하는 방식을 테스트해서 유닛 간의 상호 작용을 테스트하는 겁니다.\
컴포넌트 간의 상호 작용을 테스트하거나 마이크로 서비스 간의 상호 작용을 테스트합니다.

분리된 코드 조각이 아닌 다른 함수를 호출하는 함수가 있을 때 테스트하는 함수는 다른 함수의 결과에 따라 달라집니다.\
단일 단위 외에도 기능을 또 다른 기능에 통합하는 것을 테스트하는 것입니다.

보통 두 개의 개별로 실행되는 단위를 통합해서 실행하기 위해 사용됩니다.

### Acceptance(인수)/ End-to-end(E2E) Tests

전체 애플리케이션 또는 전체 애플리케이션 일부에 대해서 테스트합니다.

브라우저에서 수동으로 원하는 작업을 하면서도 특정한 단계를 실행하는 자동화된 스크립트를 작성하여 예상한 결과를 얻는지 확인하는 것입니다.

전체 사용자 인터페이스 테스트는 가장 복잡하기 때문에 정확히 무엇이 오류를 발생시키는지 구분하기 어려운 테스트입니다.

실제 브라우저가 필요하고 애플리케이션이 연결된 서버가 필요한 테스트 입니다.\
일반적으로 Cypress나 Selenium과 같은 특별한 도구를 사용합니다.

### Functional(기능) Tests

소프트웨어의 특정 기능을 테스트하는 겁니다.\
기능 테스트의 개념은 코드가 아닌 동작을 테스트하는 겁니다.\
테스트하는 특정 동작이나 유저 플로우와 연관된 모든 단위를 포함합니다.\
유닛이 있는 통합 테스트일 수도 유닛 테스트인 기능 테스트일 수도 있습니다.

코드 작성 방식을 리팩토링하면 동작이 동일하게 유지되는 한 테스트도 통과하게 됩니다.\
하지만 코드가 테스트와 유닛 테스트처럼 밀접하게 연결되어 있지 않아서 어떤 부분의 코드가 테스트 실패의 원인인지 정확히 알 수 없습니다.

## 테스트 도구

### Test Runner

유닛(Unit) 테스트와 통합(Integration) 테스트에서 사용되며, 테스트를 실행하고 결과를 요약해서 결과에 대한 출력을 제공합니다.

흔히 Macha와 Jest를 사용합니다.

### Assertion Library

유닛(Unit) 테스트와 통합(Integration) 테스트에서 사용되며, 테스트 논리와 예상 결과를 정의합니다.
도구를 제공하여 예상 및 테스트의 일부로써 확인하고 싶은 비교 및 조건 등을 정의합니다.

흔히 Chai와 Jest를 사용합니다.

### Headless Browser

보통 E2E 테스팅에 사용되며 Headless Browser는 코드에서 분석하기 때문에 수동으로 클릭하지 않아도 되고 브라우저 API와 DOM 등을 사용할 수 있으며, 사용자 인터페이스는 필요하지 않습니다.

흔히 Puppeteer를 사용합니다.

## CI / CD

### 파이프 라인

![pipe line](./img/pipe-line.png)

코드구축부터 배포까지 일련의 과정들을 CI / CD 파이프라인이라고 합니다.\
CI / CD 파이프라인을 구축함으로서 코드배포까지 체계적으로 만들고 테스트를 강제하여 테스트가 없으면 코드가 MERGE 되지 않게 만들 수 있습니다.

#### Continuous Integration

코드를 빌드하고 테스트하고 합칩니다.

- BUILD: 대표적인 BUILD 도구로는 Webpack이 있으며 여러가지 모듈(React, Redux, Jest 등)을 정적 자산(HTML, CSS, JS 등)으로 바꿔줍니다.

- TEST: 단위 테스트, 통합 테스트, E2E 테스트, 보안 테스트 등을 말합니다.

- MERGE: git, svn을 이용해 여러 개발자의 코드를 합치는 것을 말합니다.

#### Continuous Delivery

해당 레퍼지토리에 릴리스합니다.
main 브랜치에 최종 결과물 코드를 올립니다.

#### Continuous Deployment

이를 프로덕션, 즉 실제 서비스에 배포합니다.

## 참고 자료

- [제대로 이해하는 CI / CD | 개발자필수지식](https://www.youtube.com/watch?v=KTHZyV9yJGY)