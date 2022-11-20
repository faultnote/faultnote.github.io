---
title: Node.js 깡통 HTTP 서버 처리량 측정 POC
date: 2020-05-10
tags: 
- nodejs
- javascript
description: Node.js 런타임 인터프리터는 웹 서버가 동작하기 위한 기본적인 기능은 모두 제공하고 있습니다. 단순한 웹 서버를 만들어보고 성능을 측정 해 봅시다.
readingTime: true
author: soomtong
---

> Node.js 의 http 패키지는 한 두 개의 API 를 통해 웹 서버를 제공하고 http 요청을 할 수 있도록 해줍니다.

Node.js 런타임 인터프리터는 웹 서버가 동작하기 위한 기본적인 기능은 모두 제공하고 있습니다.
Node.js 14 버전을 기준으로 공식 메뉴얼을 살펴 보겠습니다.

![DOCS](/images/node-js-org-menu-1.png)

## http.Server 패키지

인덱스 하단 쯤에 있는 createServer 메소드를 보면 됩니다. 추가로 그 아래에 있는 get 도 사용할 계획입니다.

- [http.createServer](https://nodejs.org/dist/latest-v14.x/docs/api/http.html#http_http_createserver_options_requestlistener)
- [http.get](https://nodejs.org/dist/latest-v14.x/docs/api/http.html#http_http_get_url_options_callback)

해당 메소드는 http.Server 인스턴스를 하나 생성해줍니다.

1. 서버 인스턴스 생성
2. 생성한 서버를 특정 포트에 대기

이 두 과정을 통해 node 프로세스는 웹 서버로 대기하게 됩니다.

```js
const http = require('http')
const server = http.createServer()
server.listen(port, () => {
  console.log('started...')
})
```

http.Server 인스턴스를 생성하고 생성된 결과를 `server` 에 대입하지 않고 즉시 listen 메소드를 연결하여 코드를 줄일 수 있습니다.

```js
http.createServer(app).listen(port, () => {
  console.log('started...')
})
```

### port 번호

컴퓨터 네트워크 시스템에는 port 라는 숫자 정보를 통해 통신을 위한 채널을 열고 닫습니다. 운영체제마다 다르지만 보통 1000 번 미만의 포트는 시스템에서 사용할 수 있도록 제공되고 사용자를 위한 포트는 1000 번 이후로 사용하도록 권하고 있습니다.

웹 서버를 위한 포트는 보통 80 번을 사용합니다. 암호화 통신이 적용된 기본 포트는 443 번 입니다.

이에 대한 조금 더 자세한 내용은 다음 링크를 참고해 보세요.

- [http의 기본 포트가 80, https의 기본 포트가 443인 이유는 무엇일까?](https://johngrib.github.io/wiki/why-http-80-https-443/)

우리는 실습을 위해서 8000 번 포트를 사용하도록 하겠습니다.

```js
const port = 8000
```

## 프리티어 코드 포매팅 환경 설정

메모장이나 아무 에디터를 통해 코드를 작성할 수 있습니다. 하지만 코드의 양이 늘어나고 파일이 여러 개가 되고 특히 다른 사람들과 협업을 해야 한다면 코드와 파일을 관리하기 위한 전용 도구를 제공하고 코드를 일관적으로 포매팅하는 도구들이 필요하게 됩니다.

여러 좋은 코딩용 에디터가 있지만 최근에는 MS 의 VSCode 를 많이 추전하고 사용하고 있습니다.

- [공식 홈페이지](https://code.visualstudio.com/)
- [추천 문서 1](https://www.44bits.io/ko/keyword/visual-studio-code)
- [추천 문서 2](https://medium.com/@lazy_bug/vscode%EB%A1%9C-%EA%B0%9C%EB%B0%9C-%EC%83%9D%EC%82%B0%EC%84%B1%EC%9D%84-%EC%98%AC%EB%A6%AC%EA%B8%B0-1%EB%B6%80-3610576c267b)
- [추천 문서 3](http://blog.naver.com/PostView.nhn?blogId=bluezheng0&logNo=221634400052)

### 작업 폴더를 만들고 npm 프로젝트를 생성하기

1. http-perf 라는 그럴듯한 이름으로 폴더를 만듭니다.
2. 해당 폴더 안에서 npm init -y 를 통해 Node.js 프로젝트 구성 환경을 생성합니다.
3. 포매팅을 도와주는 prettier 라는 패키지를 설치합니다.
4. VSCode 포매터를 prettier 로 지정합니다.

```bash
http-perf 폴더 > npm init -y
http-perf 폴더 > npm i -D prettier
```

VSCode 의 커맨드 파레트를 열어 `format` 이라고 타이핑을 하게 되면 format 과 관련된 명령을 확인할 수 있습니다.

format document with 같은 명령을 선택하여 설치된 prettier 를 기본 포매터로 선택해줍니다.

처음 작성한 http.Server 파일과 새로 만든 폴더는 다음과 같이 구성합니다.

```bash
- http-perf
  + node_modules
  | http-server.js
```

## 접속 응답 제공하기

지금 작성된 웹 서버는 자체로 완성된 서버지만 아무 응답도 없어 상태를 확인할 수 없습니다.

http.Server 인스턴스는 생성시 추가로 제공되는 파라미터를 활용해 http 접속(응답 요청)이 들어왔을 때 반응하도록 설계되어 있습니다.

이 응답을 제공할 파라미터는 실행 가능한 형태로 구성되어야 하기 때문에 함수 형태로 구성합니다.
serverApp 이라는 함수를 만들어 http.Server 인스턴스에게 제공해 봅시다.

```js
const serverApp = function (req, res) {
  console.log('요청에 응답!')
  res.write(`<p>Hello world</p>`)
  res.end()
}
const server = http.createServer(serverApp)
```

### 접속해 보기

크롬, 파이어폭스 등의 웹 브라우저를 통해 접속해 응답을 확인할 수 있습니다.
Linux 또는 macOS 환경이라면 `curl` 명령을 통해 터미널에서 요청을 보낼 수 있습니다.

```bash
http-perf $ curl http://localhost:8000
```

## http.ClientRequest 패키지

Node.js 는 http 서버를 위한 기능도 내장되어 있지만 http 클라이언트를 위한 기능도 내장되어 있습니다.

> 위에서 소개한 `curl` 같은 기능을 작성할 수 있다는 뜻입니다.

이제 우리가 만드려는 http 서버 처리량 측정을 위한 방법을 눈치 챌 수 있습니다.

1. 서버를 만들고
2. 클라이언트를 만들고
3. 측량을 위한 대량의 요청을 보낸다.
4. 처리 결과를 통계한다.

요청을 위한 파일을 추가하고 코드를 작성해 보겠습니다.

```bash
- http-perf
  + node_modules
  | http-server.js
  | http-client.js
```

### http.request 사용해 보기

request 사용은 server 를 만드는 것처럼 쉽습니다.

> 그래서 옛날 웹 개발자들은 Node.js 의 등장에 열광했다고 합니다.

```js
const myServer = 'http://localhost'
const request = http.request(`${myServer}:${port}`)
request.end(() => {
  console.log('requested...')
})
```

서버가 자신의 컴퓨터에서 서비스 되고 있으니 `localhost` 를 서버 이름으로 사용합니다.
로컬 호스트는 `127.0.0.1` 이라는 예약된 숫자형 문자열로 사용될 수 있습니다.

필요에 따라서 요청에 추가 정보를 전달할 수 있지만 지금은 단순히 브라우저 주소창에 url 을 입력하는 것처럼 요청을 완료하겠습니다.

단순히 request.end() 메소드를 호출해주는 것으로 요청을 완성할 수 있습니다. 이전에 server 를 생성하고 listen() 을 통해 시작했던 것과 같은 맥락으로 이해할 수 있습니다.

server 코드를 작성했을 때와 마찬가지로 줄여 쓸 수 있습니다.

```js
http.request(`${myServer}:${port}`).end(() => {
  console.log('requested...')
})
```

### 서버가 전송한 응답 보기

웹 브라우저를 생각해보면 웹 서버가 전달해준 내용을 화면에 표시하게 됩니다. 작성 중인 클라이언트에도 이 응답을 표시하기 위해 클라이언트 앱을 개선해 보겠습니다.

위에서 서버를 만들 때처럼 클라이언트를 확장할 수 있습니다.

> 서버/클라이언트 코드를 완성해가면서 둘의 패턴이 비슷하다는 것을 느낄 것입니다. 일관성 있고 예측 가능한 코드를 작성할 수 있도록 도와준다고 볼 수 있습니다.
> 좋은 프레임워크 좋은 개발 환경은 이런 것들을 통해서 완성됩니다.

http.request 문서를 보면 추가로 받는 파라미터가 있는 것을 확인할 수 있습니다.

`clientApp` 이라는 함수를 하나 만듭니다. 이 함수는 response 객체를 나타내는 `res` 라는 식별자를 통해 서버에서 제공하는 응답 이벤트를 처리합니다.

서버는 `data` 라는 이벤트 이름과 함께 데이터를 전송합니다. 이 클라이언트 앱에서 `on()` 이라는 메소드를 통해 이벤트를 확인하고 처리하도록 합니다. 

> 이 과정은 Node.js 에서 정의한 패턴은 아니지만 많은 웹 프로그래밍 언어에서 이 패턴을 사용하고 있습니다.

```js
const clientApp = function (res) {
  res.on('data', (data) => {
    console.log('응답을 확인:', data.toString())
  })
}
```

서버를 확장했을 때와 마찬가지로 위에서 만든 함수를 파라미터로 전달하여 요청에 응답하는 앱을 추가합니다.

```js
http.request(`${myServer}:${port}`, clientApp).end(() => {
  console.log('requested...')
})
```

실행해 볼까요? 서버와 클라이언트를 독립된 창에서 수행해야 합니다.

![](/images/node-js-pref-poc-1.png)

```bash
http-perf 폴더 > node http-server.js
```

![](/images/node-js-pref-poc-2.png)

다른 창에서 클라이언트를 실행합니다.

```bash
http-perf 폴더 > node http-client.js
```

![](/images/node-js-pref-poc-3.png)

---

> POC 란 Proof of concept 의 약자 입니다.

## 접속 통계 정보 추가

이를 구현하는 방법은 아주 여러가지가 있고 천차만별의 난이도를 가지는 기법들이 있지만 단순히 시간당 요청을 카운트 하는 방법을 통해 구현합니다.

### 서버에 전역 카운트 변수를 추가하자

Node.js 프로세스가 동작하는 동안 어느 곳에서나 접근 가능한 변수를 하나 만들고 그 값을 변경 시키는 방법을 통해 카운터를 구현해 보겠습니다.

다행히 Node.js 는 멀티 스레드 프로그래밍을 지원하지 않기 때문에 다른 스레드를 통해 값을 변경할 수 없으니 이 문제는 고려하지 않겠습니다.

```js
const benchmark = { received: 0, responded: 0 }
```

### serverApp 에 카운트 추가하기

요청 받은 것과 응답 한 것을 나누는 부분은 큰 의미가 없습니다만 우선은 구분해서 사용해보겠습니다.

```js
const serverApp = function (req, res) {
  res.write(`<p>Hello world</p>`)
  res.end()
  benchmark.responded += 1
}
```

benchmark 라는 const 를 사용했지만 별도의 기본 타입 변수를 사용하려면 let 을 통해 값을 수정할 수 있는 변수를 사용할 수 있습니다.

```js
let receivedCounter = 0
```

### http.Server 인스턴스는 이벤트 리스너

서버가 받은 요청을 콘솔에 출력하기 위해 `serverApp` 에 console.log 를 찍지 않고 별도의 이벤트를 통해 로그를 남겨보기로 하겠습니다.

몇 가지 이유가 있지만 콘솔에 로그를 남기는 작업은 상대적으로 병목을 야기합니다. 따라서 우리 serverApp 의 성능을 측정하는 과정에 적절하지 않다고 볼 수 있습니다.

http.Server 의 반환 결과를 사용해 이 문제를 해결해 보겠습니다.

http.Server 의 반환 객체는 이벤트를 수신할 수 있는 구조로 되어 있습니다.
server 의 `on()` 메소드를 통해 이벤트를 감지할 수 있습니다.

```js
const server = http.createServer(serverApp)
server.on('request', (args) => {
  benchmark.received += 1
  console.log(benchmark)
})
server.listen(port, () => {
  console.log('started...')
})
```

서버가 종료되기 전까지 http 요청을 받을 때마다 `request` 이벤트를 발생시키고 server.on() 을 통해 이벤트를 감지하여 인자로 전달되는 콜백 함수를 실행합니다.

## 비동기 콜백 함수를 반복시키는 방법

프로그래밍에서 반복문을 작성하는 것은 매우 중요한 일입니다. 비동기가 아닌 환경에서 반복문을 작성하는 것은 매우 쉽습니다.

Node.js 가 제공하는 JavaScript 언어 문법에서는 for, while 등의 반복문을 사용할 수 있습니다.

```js
while (true) {
  // 아쉽게도 우리가 사용한 http.request 는 비동기 요청에 속합니다.
}
```

> 위 예제의 경우 반복문 안에 있는 비동기 요청은 영원히 실행될 수 없습니다.

Node.js 에서 비동기 요청은 마치 `다음 생에 실행할 코드` 로 이해할 수 있습니다. 비동기 요청의 콜백 함수는 다음 생에 실행된다고 볼 수 있습니다.

이에 대해서는 'Node.js 콜스택과 이벤트 루프' 등의 주제를 검색해 확인 해 볼 수 있습니다.

더 자세한 내용은 아래 링크를 확인해주세요.

- [공식 문서](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)
- [번역된 공식 문서](https://nodejs.org/ko/docs/guides/event-loop-timers-and-nexttick/)
- [JS 비동기는 어떻게 구현되어있는가?](https://velog.io/@thsoon/JS-%EB%B9%84%EB%8F%99%EA%B8%B0%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EA%B5%AC%ED%98%84%EB%90%98%EC%96%B4%EC%9E%88%EB%8A%94%EA%B0%80)
- [추천 문서 1](https://meetup.toast.com/posts/89)
- [추천 문서 1](https://tk-one.github.io/2019/02/07/nodejs-event-loop/)

### 그러면 어떻게 반복하는가

절차지향 프로그래밍에서 반복은 반복문을 통해 이루어집니다. 만약 반복문이 없는 프로그래밍 언어가 있다면 여기에서는 반복을 어떻게 해결할 수 있을까요?

이는 재귀 호출이란 방법을 통해 해결 할 수 있습니다.

함수 안에서 스스로를 호출 하는 것입니다. 우리 코드로 설명하면 이렇게 표현할 수 있습니다.

```js
http.request(`${myServer}:${port}`, clientApp).end(() => {
  console.log('requested... 이번 생...')
  http.request(`${myServer}:${port}`, clientApp).end(() => {
    console.log('requested... 다음 생...')
  })
})
```

계속 반복해야 한다면 위 요청 코드를 하나의 함수로 만들고 콜백 함수 안에서 선언한 함수가 계속 실행 되도록 구현할 수 있습니다.

```js
const request = function () {
  http.request(`${myServer}:${port}`, clientApp).end(() => {
    console.log('requested...')
    request()
  })
}
```

물론 이 함수를 실행시키기 위해 어디에서라도 `request()` 를 실행해주어야 합니다.

```js
const runRequest = function () {
  request()
}
runRequest()
```

> 콜백 지옥 입구에 오신 걸 환영합니다.
> 콜백 지옥을 벗어나는 과정도 아래에 추가할테니 계속 진행해 보겠습니다.

서버에 요청한 응답을 콘솔에 출력하지 않아도 되니 코드를 정리해 보겠습니다.

```js
const http = require('http')
const myServer = 'http://localhost', port = 8000
const request = function () {
  http.request(`${myServer}:${port}`).end(request)
}
request()
```

요청을 마치는 `end` 함수의 인자로 콜백 함수를 전달하는 것을 생각하면 위 코드처럼 줄여 쓰는 것도 가능합니다.

## 시간을 측정하기

서버에서 요청 횟수를 카운트 한 것처럼 클라이언트에서도 요청을 카운트하는 변수를 추가하겠습니다.

```js
const benchmark = { requested: 0, responded: 0 }
const clientApp = function (res) {
  res.on('data', (data) => {
    benchmark.responded += 1
  })
}
const request = function () {
  http.request(`${myServer}:${port}`, clientApp).end(() => {
    benchmark.requested += 1
    request()
    console.log(benchmark)
  })
}
```

요청을 보냈을 때와 응답을 받았을 때 각각 카운트합니다.

### 10초 간 처리량 측정

10 초간 burst 를 하고 요청을 마치고 카운트 값을 통해 우리의 깡통 http 서버의 성능을 점검하겠습니다.

Node.js 의 타이머는 두 종류가 있습니다. 자바스크립트에서 사용하던 타이머와 Node.js 시스템에서 제공하는  `process.hrtime()` 이라는 함수를 사용할 수 있습니다.

여기에서는 이 타이머 대신 매 실행 코드마다 현재 시각을 측정하여 10초를 진행하는 방법으로 시간을 재도록 하겠습니다.

이를 위한 계산은 다음과 같습니다.

```
시작 시각 = 지금
10초 후 시각 = 시작 시각 + 10 초
지금은 ? 10 초 전 : 10 초 후
```

현재 시각은 Date() 객체를 통해 얻을 수 있습니다.

```js
const MS_PER_SECOND = 1000
const startTime = Date.now()
const endTime = startTime + (10 * MS_PER_SECOND)
```

10 초가 지났는지 확인은 단순히 비교문을 통해 작성할 수 있습니다.
counterApp 이라는 함수를 만들고 `end()` 에 전달하는 방법으로 코드를 변경했습니다.

```js
const counterApp = function () {
  benchmark.requested += 1
  if (Date.now() < endTime) {
    request()
  } else {
    console.log(benchmark)
    process.exit()
  }
}
const request = function () {
  http.request(`${myServer}:${port}`, clientApp).end(counterApp)
}
```

지정된 시간이 지나면 process.exit() 를 호출해 프로그램을 종료하도록 합니다.
우선 1초로 burst 시간을 변경하고 코드를 실행해 봅니다.

![](/images/node-js-pref-poc-4.png)

## Promise 패턴

그동안 Node.js 에서 비동기 요청을 위해 콜백 함수를 사용하는 것은 흔한 일이었고 여전히 유용하게 사용되고 있습니다. 다만 콜백이 여러 겹으로 중첩되는 경우는 코드의 가독성을 떨어뜨리고 결국 유지보수를 힘들게 하는 문제를 야기합니다.

이를 위해 값을 미래에 제공하는 타입을 추가합니다. Promise 객체는 실행 즉시 약속된 값을 기대할 수 있는 형태의 객체를 반환합니다. 그리고 then() 이라는 메소드를 통해 그 값을 확인할 수 있도록 합니다. 혹시라도 오류가 발생한 경우에는 catch() 라는 메소드에게 오류 결과를 전달합니다.

- [프라미스 객체 설명](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [캡틴 판교님의 해설](https://joshua1988.github.io/web-development/javascript/promise-for-beginners/)
- [추천 문서 1](https://medium.com/@kiwanjung/%EB%B2%88%EC%97%AD-async-await-%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-%EC%A0%84%EC%97%90-promise%EB%A5%BC-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-955dbac2c4a4)

### http.get 요청을 Promise 객체로 만들기

http.request 를 get 요청에만 사용하는 경우 request().end() 구문을 get() 으로 줄일 수 있습니다.

- [http.get](https://nodejs.org/dist/latest-v14.x/docs/api/http.html#http_http_get_url_options_callback)

이 요청을 Promise 객체로 감싸보겠습니다.

> 이를 promisify 한다고 합니다.
> Node.js 에서는 promisify 를 위한 별도의 util.promisify() 함수를 제공하기도 합니다.

Promise 객체는 하나의 콜백 함수를 인자로 받습니다. 이 함수는 resolve, reject 라는 인자를 통해 비동기 실행이 성공했을 때와 실패했을 때 사용할 콜백 함수 전달합니다.

```
대상 함수1 = (성공했을 때 실행할 함수, 실패했을 때 실행할 함수) => {
  함수 본문
  성공했을 때:
    성공했을 때 실행할 함수()
  실패했을 때:
    실패했을 때 실행할 함수()
}
약속된 객체1 = new Promise(대상 함수1)
```

```js
new Promise((resolve, reject) => {
  http.get(`${myServer}:${port}`, (res) => {
    res.on('data', (data) => {
      console.log({ body: data.toString() })
    })
    res.on('end', () => {
      resolve()
    })
    res.on('error', (error) => {
      reject(error)
    })
  })
})
```

이 코드를 바탕으로 client 앱을 개선해 보겠습니다.

```js
const promisedRequest = new Promise((resolve, reject) => {
  http.get(`${myServer}:${port}`, (res) => {
    res.on('data', (data) => {
      console.log({ body: data.toString() })
    })
    res.on('end', () => {
      resolve('비동기 처리 완료')
    })
    res.on('error', (error) => {
      reject(error)
    })
  })
})
```

### Promise 객체의 비동기 호출 완성

Promise 객체는 then() 과 catch() 메소드를 통해 결과를 얻을 수 있습니다.

```js
promisedRequest.then()
```

### Promise 객체를 반환하는 함수로 개선

재귀 호출을 위해 Promise 객체를 반환하는 함수로 promisedRequest 를 개선해 보겠습니다.

```js
const promisedRequest = function () {
  return new Promise((resolve, reject) => {
    http.get(`${myServer}:${port}`, (res) => {
      res.on('data', (data) => {
        console.log({ body: data.toString() })
      })
      res.on('end', () => {
        resolve('비동기 처리 완료')
      })
      res.on('error', (error) => {
        reject(error)
      })
    })
  })
}
promisedRequest().then()
```

위와 같이 promisedRequest() 를 실행하고 다시 then() 을 실행하고 있음을 주의해야 합니다.

### 한 번 더 호출하기

요청이 완료되면 다시 request 를 호출하기 위해 then() 콜백 함수에 재귀 코드를 추가합니다.

```js
promisedRequest().then((resolved) => {
  console.log(resolved)
  promisedRequest()
})
```

### 재귀 함수로 감싸 무한히 호출하도록 변경

> 이쯤 되면 머리가 뜨거워질 수 있습니다만 거의 다 왔습니다.

이 Promise 를 반복하는 함수를 만들고 재귀 호출을 통해 무한히 http.request 하는 하도록 개선해 보겠습니다.

```js
const promisedRequestLoop = () => {
  promisedRequest().then((resolved) => {
    console.log(resolved)
    promisedRequestLoop()
  })
}
promisedRequestLoop()
```

여전히 콜백을 감싸고 있어 이해하기 어려울 수 있습니다. 여기에 추가로 비동기 호출이 실패했을 경우를 위해 catch() 를 추가해 개선해 주어야 합니다만 이 부분은 생략하겠습니다.

## Async/Await 패턴

자바스크립트 문법을 정의하는 ECMA 재단은 이 Promise 패턴을 간략히 사용할 수 있도록 해주는 async/await 문법을 2017년 도입되었습니다.

1. 만약 우리가 사용할 비동기 호출 함수가 Promise 로 감싸져 있다면 async/await 를 사용할 수 있습니다.
2. await 는 함수 앞에 붙여 주는 키워드로 비동기 호출에 대한 응답을 기다려줍니다.
3. await 키워드를 사용하려면 async 라는 키워드가 추가된 함수 안에 있어야 합니다.

위 조건을 만족시키면서 http.request 함수를 개선해 보겠습니다.

```js
const asyncRequest = async function () {
    // await 사용 가능
}
asyncRequest()
```

### 비동기 호출 해보기

위에서 사용한 promisedRequest 함수를 그대로 사용할 수 있습니다.

```js
const asyncRequest = async function () {
  await promisedRequest()
}
asyncRequest()
```

정말 단순해졌습니다. 물론 여기에도 예외처리를 위한 구문이 추가됩니다만 생략하겠습니다.

### 반복 호출하기

최근 개선 문법으로 for await 문법이 추가되었습니다만 여전히 비동기 호출 함수는 단순한 while 반복문으로 반복 실행할 수 없습니다.

재귀 함수 기법을 사용해 무한 반복 호출하는 함수를 만들어 보겠습니다.

```js
const asyncRequest = async function () {
  await promisedRequest()
  asyncRequest()
}
asyncRequest()
```

그래도 매우 단순해졌음을 확인할 수 있습니다.

```js
const runBench = async () => {
  await promisedRequest()
  benchmark.requested += 1
  const now = Date.now()
  if (now < endTime) {
    runBench()
  } else {
    console.log('requested...', benchmark)
    process.exit()
  }
}
runBench()
```

비교적 간략하게 비동기 호출을 경험하고 async/await 구문으로 개선하는 과정까지 실습을 해 보았습니다.

최근 서비스는 다양한 네트워크 서비스를 바탕으로 이루어져 있습니다. 이 중심에는 비동기 프레임워크의 등장과 확산이 큰 역할을 하고 있으며 프로그래밍 기법에 이런 패턴이 적용되며 더 다양한 방법으로 개선이 이루어지고 있습니다.

한 걸음에 모든 것을 이루기는 어렵습니다. 꾸준히 계족 자신의 보폭으로 길을 잃지 않기를 기대합니다.


