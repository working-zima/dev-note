# Rendering

```plaintext
It only works in a Client Component but none of its parents are marked with "use client", so they're Server Components by default.
```

Next.js는 서버 컴포넌트와 리액트 서버 컴포넌트 그리고 클라이언트 컴포넌트가 있습니다.

creat React app이나 Vite의 도움을 받아 만드는 모든 바닐라 리액트 앱들에서 기본적으로 클라이언트 컴포넌트를 사용하고 있습니다.\
React.js는 순수한 클라이언트 사이드 라이브러리로 브라우저에서 클라이언트 측에서 코드를 실행합니다.

Next.js는 풀스택 프레임워크이기 때문에 프론트엔드 뿐 아니라 백엔드도 있습니다.\
기본적으로 Next.js에서 모든 컴포넌트는 서버 컴포넌트입니다.\
따라서 Next.js와 작업할 때 코드는 백엔드에서도 실행됩니다.

## 리액트 서버 컴포넌트

모든 리액트 컴포넌트들은 그것들이 페이지인지, 레이아웃인지, 기본 컴포넌트인지에 상관없이 브라우저가 아닌 오직 서버에서만 렌더링됩니다.\
이것을 리액트 서버 컴포넌트라 부릅니다.\
예시로 `console.log()`를 사용해도 브라우저 개발자 툴에서 실행 로그를 확인할 수 없으며 터미널에서만 확인할 수 있습니다.

### 사용 이유

서버 컴포넌트를 사용하면 다운로드해야 하는 클라이언트 측의 자바스크립트 코드가 줄어들 수 있어 웹사이트의 성능을 향상시킬 수 있습니다.\
웹 검색 크롤러들은 완성 컨텐츠를 포함하는 페이지들을 볼 수 있기 때문에 검색엔진 최적화에도 좋습니다.

### 특징

- `Event Listener` 사용 불가
- `React Hooks`사용 불가
- 컴포넌트를 `async` 함수로 정의 가능

## 클라이언트 컴포넌트

어떤 사용자 상호작용을 기다리고 있는 부분은 클라이언트에서 실행되는 코드가 필요하므로 클라이언트 컴포넌트여야 합니다.\
상호작용에 사용하는 eventHandler, 리액트 훅들은 서버 측에서는 사용 불가하기 때문에 클라이언트 컴포넌트를 사용해야합니다.

### 클라이언트 컴포넌트 사용 방법

클라이언트 컴포넌트를 만들고 싶다면 Next.js에게 컴포넌트를 잡고 있는 파일 위에 특별한 지시어를 사용하여 드러나게 알려야 합니다.\
`'use Client'` 지시어를 사용하면 useClient 사이트 기능을 컴포넌트 내에 사용할 수 있습니다.

```tsx
'use client'

import { useEffect, useState } from 'react';
import Image from 'next/image';

import burgerImg from '@/assets/burger.jpg';
import curryImg from '@/assets/curry.jpg';
import dumplingsImg from '@/assets/dumplings.jpg';
import macncheeseImg from '@/assets/macncheese.jpg';
import pizzaImg from '@/assets/pizza.jpg';
import schnitzelImg from '@/assets/schnitzel.jpg';
import tomatoSaladImg from '@/assets/tomato-salad.jpg';
import classes from './image-slideshow.module.css';

const images = [
  { image: burgerImg, alt: 'A delicious, juicy burger' },
  { image: curryImg, alt: 'A delicious, spicy curry' },
  { image: dumplingsImg, alt: 'Steamed dumplings' },
  { image: macncheeseImg, alt: 'Mac and cheese' },
  { image: pizzaImg, alt: 'A delicious pizza' },
  { image: schnitzelImg, alt: 'A delicious schnitzel' },
  { image: tomatoSaladImg, alt: 'A delicious tomato salad' },
];

export default function ImageSlideshow() {
  const [currentImageIndex, setCurrentImageIndex] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setCurrentImageIndex((prevIndex) =>
        prevIndex < images.length - 1 ? prevIndex + 1 : 0
      );
    }, 5000);

    return () => clearInterval(interval);
  }, []);

  return (
    <div className={classes.slideshow}>
      {images.map((image, index) => (
        <Image
          key={index}
          src={image.image}
          className={index === currentImageIndex ? classes.active : ''}
          alt={image.alt}
        />
      ))}
    </div>
  );
}
```

## 캐싱

NextJS는 안보이는곳에서 굉장히 공격적인 캐싱을 합니다.\
유저가 들어간 페이지들의 데이터까지 모두 캐싱합니다\
그래서 다른 페이지에 갔다가 돌아온다면 캐시에서 존재하고 있는 페이지를 로딩하여 최대한 빨리 보여지게 합니다.\
페이지를 새로고침할때만, 그러니까 본질적으로 이 페이지를 떠났다가 다시 돌아올때만 페이지가 다시 설계됩니다.
