+++
title = "Bun Client Build"
date = "2025-11-28T08:41:52+09:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "soomtong"
authorTwitter = "" #do not include @
cover = ""
tags = ["bun", "react", "client", "build"]
keywords = ["bun", "react", "client", "build"]
description = "간단하게 다른 종속성 없이 Bun으로 JSX 빌드 및 통합 서버 구축하기"
showFullContent = false
readingTime = false
hideComments = false
+++

## Bun으로 JSX 빌드 및 통합 서버 구축하기

Bun에는 별도의 설정(Babel, Webpack 등) 없이도 JSX/TSX 파일을 직접 변환할 수 있는 번들러가 내장되어 있습니다. 또한 Bun.serve를 사용하면 정적 파일 서빙과 API 서버를 하나로 통합할 수 있습니다.

1. 프로젝트 구조

```
/bun-client-server
[ 608]  .
├── [ 192]  archive
├── [    ]  dist -------- 빌드된 정적 파일이 위치하는 폴더
├── [2.4K]  bun.lock
├── [5.5K]  CLAUDE.md
├── [ 128]  client
│   ├── [5.3K]  app.tsx
│   └── [2.9K]  styles.css
├── [ 550]  index.ts
├── [ 574]  package.json
├── [ 128]  public
├── [1.9K]  README.md
├── [ 352]  server
│   ├── [4.9K]  app.test.ts
│   ├── [2.2K]  app.ts
│   ├── [4.8K]  archive.test.ts
│   ├── [6.9K]  archive.ts
│   ├── [ 712]  db.schema.ts
│   ├── [ 12K]  db.test.ts
│   ├── [6.9K]  db.ts
│   ├── [2.1K]  render.tsx
│   └── [1.4K]  zip.ts
└── [ 830]  tsconfig.json
```

2. 소스 코드

사용되는 종속성 패키지는 React와 ReactDOM 뿐이며, Bun이 자체적으로 JSX 변환을 처리합니다.

```js
// package.json
"dependencies": {
  "react": "^19.2.0",
  "react-dom": "^19.2.0"
},
"devDependencies": {
  "@types/bun": "latest",
  "@types/react": "^19.2.6",
  "@types/react-dom": "^19.2.3"
},
```

`/view/index.html`

진입점 HTML 파일입니다. 스크립트 경로가 /app.js를 가리키는지 확인하세요.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bun JSX & Server App</title>
</head>
<body>
    <div id="root"></div>
    <!-- Bun이 빌드한 파일을 로드합니다 -->
    <script type="module" src="./app.js"></script>
</body>
</html>
```

`/client/app.jsx`

React 메인 진입점입니다. 서버에서 제공하는 동적 데이터를 가져오는 예시를 추가했습니다.

```jsx
import React, { useState, useEffect } from 'react';
import { createRoot } from 'react-dom/client';

const App = () => {
  const [count, setCount] = useState(0);
  const [serverMsg, setServerMsg] = useState('데이터 로딩 중...');

  useEffect(() => {
    // 통합 서버의 동적 API 호출
      const params = new URLSearchParams();
      const currentCursor = cursorRef.current;
      if (currentCursor) params.set('cursor', currentCursor);
      params.set('limit', '50');

      fetch(`/list?${params}`)
      .then(res => res.json())
      .then(data => setServerMsg(data.message));
  }, []);

  return (
    <div style={{ fontFamily: 'sans-serif', padding: '20px' }}>
      <h1>Hello from Bun! 🚀</h1>
      <p>이 JSX는 Bun으로 번들링되었습니다.</p>
      
      <div style={{ border: '1px solid #ccc', padding: '10px', margin: '10px 0' }}>
        <h3>서버 메시지 (동적):</h3>
        <p>{serverMsg}</p>
      </div>

      <button onClick={() => setCount(count + 1)}>
        카운트: {count}
      </button>
    </div>
  );
};

const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

3. 프로젝트 빌드

```ts
// index.ts
// 서버 시작 전 archive 폴더 스캔
await scanArchiveFolder();

// 번들 빌드
await Bun.build({
  entrypoints: ['./client/app.jsx'],
  outdir: './dist',
  minify: true,
  sourcemap: "external",
});

console.log("빌드 완료! ✅");

// 서버 시작
Bun.serve(createApp(port));
```

4. 통합 서버 실행 (app.js)

이제 정적 파일(HTML, JS)을 서빙하면서 동시에 API 요청도 처리하는 서버를 만듭니다.

```ts
// app.js
const server = Bun.serve({
  port: 3000,
  routes: {
    "/": {
      GET: async () => {
        // 루트 경로 요청 시 index.html 반환
        return new Response(Bun.file("../view/index.html"), {
            headers: { "Content-Type": "text/html" },
        });
    }

    // 빌드된 JS 파일 및 기타 정적 파일 서빙 (/dist/...)
    if (url.pathname.startsWith("/dist/")) {
      const filePath = "." + url.pathname;
      return new Response(Bun.file(filePath));
    }

        return renderIndexPage();
      },
    },

    "/dist/styles.css": Bun.file("client/styles.css"),
    "/dist/app.js": Bun.file("client/app.tsx"),

    "/.ping": Response.json({ data: "hello world" }),

  },
});
```

Bun의 내장 번들러와 서버 기능을 활용하면, React 클라이언트와 API 서버를 하나의 애플리케이션으로 쉽게 통합할 수 있습니다. vite, Next.js 같은 복잡한 설정 없이도 빠르게 개발을 시작할 수 있습니다.
