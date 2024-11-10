# Utility-First Fundamentals

제약된 기본 유틸리티 집합을 사용하여 복잡한 컴포넌트를 구축하는 방법.

## 개요

전통적으로 웹에서 스타일을 적용하려면 CSS를 작성해야 했습니다.

### 커스텀 디자인에 맞춰 커스텀 CSS가 필요한 전통적인 접근 방법

![utility-first-foundamentals-01](./img/utility-first-foundamentals-01.png)

```html
<div class="chat-notification">
  <div class="chat-notification-logo-wrapper">
    <img class="chat-notification-logo" src="/img/logo.svg" alt="ChitChat Logo">
  </div>
  <div class="chat-notification-content">
    <h4 class="chat-notification-title">ChitChat</h4>
    <p class="chat-notification-message">You have a new message!</p>
  </div>
</div>

<style>
  .chat-notification {
    display: flex;
    align-items: center;
    max-width: 24rem;
    margin: 0 auto;
    padding: 1.5rem;
    border-radius: 0.5rem;
    background-color: #fff;
    box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04);
  }
  .chat-notification-logo-wrapper {
    flex-shrink: 0;
  }
  .chat-notification-logo {
    height: 3rem;
    width: 3rem;
  }
  .chat-notification-content {
    margin-left: 1.5rem;
  }
  .chat-notification-title {
    color: #1a202c;
    font-size: 1.25rem;
    line-height: 1.25;
  }
  .chat-notification-message {
    color: #718096;
    font-size: 1rem;
    line-height: 1.5;
  }
</style>
```

Tailwind을 사용하면, HTML에서 직접 미리 정의된 클래스들을 적용하여 요소를 스타일링합니다.

### CSS를 작성하지 않고 유틸리티 클래스를 사용하여 커스텀 디자인 만들기

![utility-first-foundamentals-01](./img/utility-first-foundamentals-01.png)

```html
<div class="p-6 max-w-sm mx-auto bg-white rounded-xl shadow-lg flex items-center gap-x-4">
  <div class="shrink-0">
    <img class="size-12" src="/img/logo.svg" alt="ChitChat Logo">
  </div>
  <div>
    <div class="text-xl font-medium text-black">ChitChat</div>
    <p class="text-slate-500">You have a new message!</p>
  </div>
</div>
```

위 예제에서는 다음을 사용했습니다:

- Tailwind의 flexbox 및 패딩 유틸리티(`flex`, `shrink-0`, `p-6`)를 사용하여 전체 카드 레이아웃 제어
- 최대 너비 및 마진 유틸리티(`max-w-sm`, `mx-auto`)로 카드 너비를 제한하고 수평으로 중앙 정렬
- 배경색, 테두리 반지름, 그림자 유틸리티(`bg-white`, `rounded-xl`, `shadow-lg`)로 카드 외관 스타일링
- 이미지 크기 유틸리티(`size-12`)로 로고 이미지의 너비와 높이 설정
- 간격 유틸리티(`gap-x-4`)로 로고와 텍스트 사이의 간격 처리
- 글꼴 크기, 텍스트 색상, 글꼴 두께 유틸리티(`text-xl`, `text-black`, `font-medium` 등)로 카드 텍스트 스타일링

이 방식으로 CSS를 한 줄도 작성하지 않고 완전히 커스텀 컴포넌트를 디자인할 수 있습니다.

이제 여러분은 이렇게 생각하고 있을 겁니다, _"이건 참 난잡하고 끔찍해!"_ 맞습니다. 처음에는 이게 좋은 아이디어라고 생각하기 어려울 겁니다. **하지만 실제로 시도해보면** 많은 중요한 장점들을 깨닫게 될 겁니다:

- **클래스 이름을 발명하는 데 에너지를 낭비하지 않습니다.** 더 이상 `sidebar-inner-wrapper` 같은 이상한 클래스 이름을 추가할 필요가 없고, 실제로는 그저 flex 컨테이너인 것을 위한 완벽한 추상 이름을 고민할 필요도 없습니다.
- **CSS 파일이 커지지 않습니다.** 전통적인 방식에서는 새로운 기능을 추가할 때마다 CSS 파일이 커집니다. 유틸리티를 사용하면 모든 것이 재사용 가능하므로 새로운 CSS를 작성할 일이 거의 없습니다.
- **변경이 더 안전하게 느껴집니다.** CSS는 전역적이기 때문에 변경할 때 무엇이 깨질지 알 수 없습니다. 하지만 HTML의 클래스는 로컬적이기 때문에 변경하더라도 다른 부분이 깨질 염려가 없습니다.

미리 정의된 유틸리티 클래스를 사용하여 HTML만으로 작업하는 방식이 얼마나 생산적인지 깨닫게 되면, 다른 방식으로 작업하는 것은 고통처럼 느껴질 겁니다.

## 인라인 스타일을 사용하지 않는 이유는?

이 방식에 대한 일반적인 반응은 "이게 인라인 스타일이랑 같지 않나요?"라는 질문입니다. 어느 정도 맞는 말입니다. 요소에 스타일을 직접 적용하는 방식이기 때문입니다.

하지만 유틸리티 클래스를 사용하는 것이 인라인 스타일보다 몇 가지 중요한 장점이 있습니다:

- **제약을 두고 디자인하기.** 인라인 스타일을 사용하면 모든 값이 마법의 숫자가 됩니다. 하지만 유틸리티를 사용하면 미리 정의된 디자인 시스템에서 스타일을 선택하게 되어 시각적으로 일관된 UI를 쉽게 만들 수 있습니다.
- **반응형 디자인.** 인라인 스타일에서는 미디어 쿼리를 사용할 수 없지만, Tailwind의 반응형 유틸리티를 사용하면 반응형 인터페이스를 쉽게 만들 수 있습니다.
- **Hover, focus, 기타 상태 처리.** 인라인 스타일에서는 hover나 focus와 같은 상태를 처리할 수 없지만, Tailwind의 상태 변형자를 사용하면 상태에 맞는 스타일을 쉽게 적용할 수 있습니다.

다음은 완전히 반응형이고 hover 및 focus 스타일이 포함된 버튼을 가진 컴포넌트로, 유틸리티 클래스로만 만들어졌습니다:

![utility-first-foundamentals-02](./img/utility-first-foundamentals-02.png)

```html
<div class="py-8 px-8 max-w-sm mx-auto space-y-2 bg-white rounded-xl shadow-lg sm:py-4 sm:flex sm:items-center sm:space-y-0 sm:gap-x-6">
  <img class="block mx-auto h-24 rounded-full sm:mx-0 sm:shrink-0" src="/img/erin-lindford.jpg" alt="Woman's Face" />
  <div class="text-center space-y-2 sm:text-left">
    <div class="space-y-0.5">
      <p class="text-lg text-black font-semibold">
        Erin Lindford
      </p>
      <p class="text-slate-500 font-medium">
        Product Engineer
      </p>
    </div>
    <button class="px-4 py-1 text-sm text-purple-600 font-semibold rounded-full border border-purple-200 hover:text-white hover:bg-purple-600 hover:border-transparent focus:outline-none focus:ring-2 focus:ring-purple-600 focus:ring-offset-2">Message</button>
  </div>
</div>
```

## 유지보수 문제

유틸리티 우선 접근 방식을 사용할 때 가장 큰 유지보수 문제는 자주 반복되는 유틸리티 조합을 관리하는 것입니다.

이 문제는 컴포넌트나 파셜을 추출하고, 멀티 커서 편집 및 간단한 루프와 같은 에디터 및 언어 기능을 사용하여 쉽게 해결할 수 있습니다.

```html
<!-- PrimaryButton.vue -->
<template>
  <button class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
    <slot/>
  </button>
</template>
```

그 외에는, 유틸리티 우선 CSS 프로젝트를 유지보수하는 것이 대규모 CSS 코드베이스를 유지보수하는 것보다 훨씬 쉬운 일임을 알게 될 겁니다. GitHub, Netflix, Heroku, Kickstarter, Twitch, Segment와 같은 대기업들이 이 방식을 사용하여 큰 성공을 거두고 있습니다.

이 방식에 대해 다른 사람들이 겪은 경험을 듣고 싶다면 다음 리소스를 확인하세요:

- [By The Numbers: A Year and a Half with Atomic CSS](https://medium.com/@johnpolacek/by-the-numbers-a-year-and-half-with-atomic-css-39d75b1263b4) by John Polacek
- [No, Utility Classes Aren't the Same As Inline Styles](https://frontstuff.io/no-utility-classes-arent-the-same-as-inline-styles) by Sarah Dayan of Algolia
- [Diana Mounter on using utility classes at GitHub](http://www.fullstackradio.com/75), a podcast interview

더 많은 내용을 원하시면 [The Case for Atomic/Utility-First CSS](https://johnpolacek.github.io/the-case-for-atomic-css/)를 확인하세요. [John Polacek](https://x.com/johnpolacek).
