---
title: Next.js에 Styled-components 적용하기

date: 2020-07-26 22:50:02
tags: [react, next]
categories: React
---

Next.js에 Styled Components를 적용하고 새로고침을 하면 콘솔에 아래와 같이 나온다.

![/blog/images/2020-07-26-next-styled-components/2020-07-11__12.06.58.png](/blog/images/2020-07-26-next-styled-components/2020-07-11__12.06.58.png)

왜일까? 🤔
server와 client 어쩌고 나오는 것을 보니 ssr 문제처럼 보인다.
구글링을 해보자.

![/blog/images/2020-07-26-next-styled-components/2020-07-11__1.23.42.png](/blog/images/2020-07-26-next-styled-components/2020-07-11__1.23.42.png)

[https://github.com/vercel/next.js/tree/canary/examples/with-styled-components](https://github.com/vercel/next.js/tree/canary/examples/with-styled-components)

관련 예제가 있다. (살았다.)
예제의 목적은 요약하면 다음과 같다.

- <Document />를 확장하고 <head>에 서버 사이드 렌더링된 스타일을 주입
- 서버 사이드 렌더링에 필요한 babel-plugin-styled-components 플러그인을 추가

# 적용 방법

## 1. babel 플러그인 설치 및 설정

babel-plugin-styled-components를 설치한다.

```
npm install --save-dev babel-plugin-styled-components
```

그리고 다음과 같이 .babelrc 설정을 해주자.

```
{
  "presets": ["next/babel"],
  "plugins": [["styled-components", { "ssr": true }]]
}
```

## 2. \_document.js 파일 생성

pages 폴더에 \_document.js 파일을 생성한다. 이 파일에서 <Documents />를 확장해 <head>에 서버 사이드 렌더링된 스타일을 주입한다.
한마디로 next.js의 index.html head에 서버 사이드 렌더링 스타일을 주입시켜 주는 것이다.

```jsx
import Document from "next/document";
import { ServerStyleSheet } from "styled-components";

export default class MyDocument extends Document {
  static async getInitialProps(ctx) {
    const sheet = new ServerStyleSheet();
    const originalRenderPage = ctx.renderPage;

    try {
      // sheet을 사용해 정의된 모든 스타일을 수집
      ctx.renderPage = () =>
        originalRenderPage({
          enhanceApp: (App) => (props) =>
            sheet.collectStyles(<App {...props} />),
        });

      // Documents의 initial props
      const initialProps = await Document.getInitialProps(ctx);

      // props와 styles를 반환
      return {
        ...initialProps,
        styles: (
          <>
            {initialProps.styles}
            {sheet.getStyleElement()}
          </>
        ),
      };
    } finally {
      sheet.seal();
    }
  }
}
```

Styled component에서 제공하는 ServerStyleSheet를 활용해 정의된 모든 스타일을 수집하고, 페이지가 렌더링되기 전에 props와 styles를 채워준다. 수집된 스타일은 getStyleElement 메소드로 `<style>` 태그에 감싸져 head에 뿌려지게 된다.
