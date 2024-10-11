# Image

Next.js에서는 특별한 내장 이미지 요소가 있고 이것은 더 최적화된 방법으로 이미지를 출력할 수 있게 도와줍니다.\
예를 들어 페이지에서 실제로 보이는 경우에만 이미지가 표시되도록 이미지를 지연 로딩하여 구현합니다./
추가적인 구성 없이 자동적으로 이를 실행해줍니다.

```tsx
// app/page.js

import Image from 'next/image'

export default function Page() {
  return (
    <Image src={logoImg} alt="A plate with food on it" />
  )
}
```

html의 이미지 요소를 보면 추가되어 있는 속성을 볼 수 있습니다.

`loading="lazy"`은 이미지가 지연로딩되도록 합니다.\
`width="1024" height="1024"` 자동적으로 너비와 높이를 추론되어 있으며, 원한다면 덮어 쓸 수 있습니다.\
`srcset`으로 포트와 웹사이트를 방문하는 기기에 따라 크기가 조정된 이미지가 로딩되도록 보장하며, 자동적으로 사용자에 의해 사용되는 브라우저에 가장 알맞는 파일 포맷으로 이미지를 서브합니다.

```html
<img alt="A plate with food on it" loading="lazy" width="1024" height="1024" decoding="async" data-nimg="1" style="color:transparent" srcset="/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Flogo.5daadb4d.png&amp;w=1080&amp;q=75 1x, /_next/image?url=%2F_next%2Fstatic%2Fmedia%2Flogo.5daadb4d.png&amp;w=2048&amp;q=75 2x" src="/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Flogo.5daadb4d.png&amp;w=2048&amp;q=75">
```

## Props

다음은 `Image` 컴포넌트에서 사용할 수 있는 `props`의 요약입니다.

Prop | Example | Type | Status
:-: | :-: | :-: | :-:
`src` | `src="/profile.png"` | String | Required
`width` | `width={500}` | Integer (px) | Required
`height` | `height={500}` | Integer (px) | Required
`alt` | `alt="Picture of the author"` | String | Required
`loader` | `loader={imageLoader}` | Function | -
`fill` | `fill={true}` | Boolean | -
`sizes` | `sizes="(max-width: 768px) 100vw, 33vw"` | String | -
`quality` | `quality={80}` | Integer (1-100) | -
`priority` | `priority={true}` | Boolean | -
`placeholder` | `placeholder="blur"` | String | -
`style` | `style={{objectFit: "contain"}}` | Object | -
`onLoadingComplete` | `onLoadingComplete={img => done())}` | Function | Deprecated
`onLoad` | `onLoad={event => done())}` | Function | -
`onError` | `onError(event => fail()}` | Function | -
`loading` | `loading="lazy"` | String | -
`blurDataURL` | `blurDataURL="data:image/jpeg..."` | String | -
`overrideSrc` | `overrideSrc="/seo.png"` | String | -

## priority 경고

`Image` 컴포넌트를 사용하다보면 이미지가 페이지를 로딩할 때 항상 보여서, 해당 이미지가 화면 내에서 용량을 많이 차지하기 때문에 `priority` 속성을 추가해달라는 경고를 볼 수 있습니다.

```plaintext
// 경고

warn-once.js:16 Image with src "/_next/static/media/logo.5daadb4d.png" was detected as the Largest Contentful Paint (LCP). Please add the "priority" property if this image is above the fold.
```

만약 로고처럼 첫 로딩 시 보이는 이미지라면 레이지로딩을 할 필요가 없습니다.\
`priority` 속성을 다음과 같이 추가하여 우선적으로 로딩되도록 하면 됩니다.

```tsx
// app/page.js

import Image from 'next/image'

export default function Page() {
  return (
    <Image src={logoImg} alt="A plate with food on it" priority />
  )
}
```
