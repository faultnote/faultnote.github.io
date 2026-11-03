+++
title = "Browser History Brief"
date = "2026-09-02T00:00:00+09:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "soomtong"
authorTwitter = "soomtong" #do not include @
cover = ""
tags = ["browser", "web", "history", "electron", "tauri"]
keywords = ["browser history", "browser internals", "browser war", "chromium", "dia", "electron", "tauri"]
description = "인터넷의 탄생부터 브라우저 인터널, 브라우저 전쟁, Dia 브라우저, 그리고 Electron·Tauri 같은 브라우저의 외적 확장까지를 한 흐름으로 정리한다."
showFullContent = false
readingTime = true
hideComments = false
+++

> 인터넷의 탄생부터 브라우저 인터널, 브라우저 전쟁, Dia 브라우저,
> 그리고 Electron·Tauri 같은 "브라우저의 외적 확장"까지를 한 흐름으로 정리한다.

최종 수정: 2026-09-02

## 목차

1. [인터넷의 탄생 — 왜, 어떻게, 어떤 환경에서](#1-인터넷의-탄생)
2. [웹의 탄생 — 브라우저, HTML, CSS, JavaScript](#2-웹의-탄생)
3. [브라우저 인터널 — 주소창에서 픽셀까지](#3-브라우저-인터널)
4. [브라우저의 역사와 브라우저 전쟁](#4-브라우저의-역사와-브라우저-전쟁)
5. [브라우저는 새로운 운영체제다](#5-브라우저는-새로운-운영체제다)
6. [Dia 브라우저 — AI 네이티브 브라우저](#6-dia-브라우저)
7. [브라우저의 외적 확장 — Electron, Tauri](#7-브라우저의-외적-확장)
8. [연표 한눈에 보기](#8-연표-한눈에-보기)
9. [참고 자료](#9-참고-자료)

---

## 1. 인터넷의 탄생

### 1.1 개요 — 왜 만들어졌나

인터넷의 직접 조상은 1969년 미국 국방부 ARPA(고등연구계획국)가 만든 `ARPANET`이다.
흔히 "핵전쟁에 살아남기 위한 망"으로 설명되지만, 이는 절반만 맞다.

- 연구 자원 공유가 1차 동기였다. 1960년대 컴퓨터는 대학·연구소마다 한 대씩 있는
  값비싼 시분할(time-sharing) 기계였다. ARPA IPTO(정보처리기술실)의 리클라이더(J.C.R. Licklider)는
  1962년 메모에서 이 기계들을 잇는 "은하계 컴퓨터 네트워크"를 상상했고,
  후임 로버츠(Lawrence Roberts)가 이를 실제 프로젝트로 만들었다.
- 생존성(survivability)은 패킷 교환 이론의 동기였다. RAND의 폴 배런(Paul Baran)은
  1964년 『On Distributed Communications』에서 중앙 없는 분산망과 메시지 블록 교환을 제안했고,
  영국 NPL의 도널드 데이비스(Donald Davies)는 독립적으로 같은 아이디어에 "패킷(packet)"이라는
  이름을 붙였다(1965). UCLA의 클라인록(Leonard Kleinrock)은 큐잉 이론으로 패킷망을 수학적으로 분석하는 토대를 놓았다.

핵심은 회선 교환(circuit switching)에서 패킷 교환(packet switching)으로의 전환이다.
전화망은 통화 동안 회선을 독점하지만, 패킷망은 데이터를 잘게 잘라 각자 최선의 경로로 보낸다.
이 선택이 이후 모든 것을 결정했다.

### 1.2 당시의 환경

#### 컴퓨팅 환경 (1969년 전후)

| 항목 | 상태 |
| --- | --- |
| 컴퓨터 | 메인프레임·미니컴퓨터. UCLA는 SDS Sigma 7, SRI는 SDS 940, 유타는 DEC PDP-10 |
| 메모리 | 수십 K 워드 단위. 라우터 격인 IMP(Honeywell DDP-516)는 12K 워드(약 24KB) |
| 입출력 | 텔레타이프(ASR-33), 천공카드, 종이테이프. 그래픽 터미널은 극히 드묾 |
| OS | 시분할 OS(CTSS, Multics, TENEX). Unix는 같은 해(1969) 벨 연구소 PDP-7에서 태어남 |
| 언어 | 어셈블리, Fortran, BCPL. C는 1972년에야 등장 |
| 개인용 컴퓨터 | 없음. Altair 8800이 1975년, IBM PC가 1981년 |

#### 네트워크 환경

| 항목 | 상태 |
| --- | --- |
| 회선 | AT&T 전용선 50 kbps. (오늘 가정용 광랜의 약 2만분의 1) |
| 노드 | 1969년 12월 4개(UCLA, SRI, UCSB, Utah). 1971년 15개, 1973년 국제 연결(노르웨이 NORSAR, 영국 UCL) |
| 첫 메시지 | 1969-10-29, UCLA→SRI로 `LOGIN`을 보내다 `LO`에서 SRI 쪽이 다운. 찰리 클라인(Charley Kline)의 기록 |
| 모뎀 | 음향 커플러 300 baud. 가정 접속은 사실상 없음 |
| 프로토콜 | 1970년 NCP(Network Control Program). TCP/IP는 아직 없음 |

#### 개발 환경과 문화

- IMP(Interface Message Processor): BBN이 만든 최초의 "라우터". 호스트 컴퓨터는 서로 다른 기종이라
  직접 통신할 수 없었으므로, 각 호스트 앞에 동일한 소형 컴퓨터를 두어 패킷 처리를 맡겼다.
  이 "호스트와 망을 분리한다"는 계층화 발상이 지금의 라우터·NIC 구조로 이어진다.
- RFC(Request for Comments): 1969-04-07 스티브 크로커(Steve Crocker)가 쓴 RFC 1 "Host Software"가 시작.
  대학원생들이 권위자를 자처하기 부담스러워 "의견을 구합니다"라고 이름 붙였다.
  이 겸손한 이름이 열린 표준 문화의 뿌리가 되었고, HTTP(RFC 1945/2616)와 HTML 2.0(RFC 1866)도 RFC로 태어난다.
- 네트워크 워킹 그룹(NWG): 상명하달이 아닌 합의 기반. 이후 IETF(1986)로 제도화된다.
  "We reject kings, presidents and voting. We believe in rough consensus and running code."(David Clark, 1992)

### 1.3 핵심 디자인 — TCP/IP와 종단 간 원칙

ARPANET은 성공했지만 NCP는 ARPANET 내부에서만 통했다. 패킷 무선망(PRNET), 위성망(SATNET)이 생기자
서로 다른 망을 잇는(inter-net) 프로토콜이 필요해졌다.

- 1973년 빈트 서프(Vint Cerf)와 밥 칸(Bob Kahn)이 TCP를 설계. 1974년 논문
  『A Protocol for Packet Network Intercommunication』, 같은 해 RFC 675에서 "Internet"이라는 단어 등장.
- 1978년 TCP를 TCP(신뢰성)와 IP(라우팅)로 분리. 이 분리로 UDP 같은 다른 전송 계층이 가능해졌다.
- 1983-01-01 플래그 데이(Flag Day): ARPANET 전체가 NCP를 끄고 TCP/IP로 전환.
- 1983년 폴 모카페트리스(Paul Mockapetris)의 DNS(RFC 882/883). 이전에는 SRI가 관리하는 `HOSTS.TXT`
  파일 하나를 모두가 FTP로 받아 갔다.
- 1983년 4.2BSD Unix에 TCP/IP 스택 탑재(빌 조이). 이 구현이 소켓 API(`socket`, `bind`, `connect`)를
  정의했고, 오늘날 모든 OS의 네트워크 API가 이 형태다.

종단 간 원칙(end-to-end principle): 망은 단순하게(dumb pipe) 두고, 지능은 끝단(호스트)에 둔다.
IP는 "최선을 다해 배달(best effort)"만 하고 순서·재전송은 TCP가 양 끝에서 처리한다.
전화망(똑똑한 망, 멍청한 단말)과 정반대의 설계이며, 이 덕분에 망을 바꾸지 않고도 웹, 음성, 영상 같은
새 응용이 끝단에서 얼마든지 태어날 수 있었다.

~~~text
# 1983년 이후 인터넷의 계층 (TCP/IP 모델)

  응용   : HTTP, SMTP, DNS, FTP, SSH      ← 브라우저가 사는 곳
  전송   : TCP, UDP (그리고 2021년 QUIC)
  인터넷 : IP (IPv4 1981, IPv6 1998)
  링크   : Ethernet(1973 PARC → 1983 IEEE 802.3), Wi-Fi(1997), LTE ...
~~~

### 1.4 pros vs cons

패킷 교환 + TCP/IP의 장단점.

- 장점: 회선 효율, 부분 장애 내성, 이기종 망 연결, 끝단 혁신의 자유(허가 없는 혁신)
- 단점: 실시간 보장 없음(지연·지터), 보안이 설계에 없음(IP 스푸핑, 평문), 주소 고갈(IPv4), 혼잡 제어를 나중에 덧댐(1988 반 제이컵슨)

### 1.5 그 후 — 상용화까지

| 연도 | 사건 |
| --- | --- |
| 1971 | 레이 톰린슨(Ray Tomlinson), 이메일과 `@` 기호 |
| 1972 | ICCC에서 ARPANET 첫 공개 시연 |
| 1979 | Usenet (뉴스그룹) |
| 1985 | NSFNET 시작. 학술망 백본. 56kbps → T1(1988) → T3(1991) |
| 1988 | 모리스 웜. 인터넷 보안이 사회 문제로 |
| 1989 | 최초의 상용 ISP(The World). 같은 해 CERN에서 팀 버너스리가 웹 제안 |
| 1990 | ARPANET 해체 |
| 1991 | Gopher(미네소타대) — 웹의 마지막 경쟁자 |
| 1995 | NSFNET 해체, 상업 이용 전면 개방. 웹과 넷스케이프의 해 |

---

## 2. 웹의 탄생

### 2.1 개요 — CERN, 1989

팀 버너스리(Tim Berners-Lee)는 CERN의 계약직 소프트웨어 엔지니어였다. 문제는 인터넷이 아니라
정보 관리였다. 수천 명의 물리학자가 서로 다른 컴퓨터, 서로 다른 문서 포맷으로 일하고,
사람이 떠나면 지식이 사라졌다.

- 1980년 개인 프로젝트 ENQUIRE: 카드 사이를 링크로 잇는 하이퍼텍스트 도구.
- 1989년 3월 『Information Management: A Proposal』. 상사 마이크 센달(Mike Sendall)의 메모:
  "Vague but exciting..."
- 그가 결합한 기존 기술 세 가지: 하이퍼텍스트(테드 넬슨의 Xanadu, 더그 엥겔바트의 NLS, HyperCard),
  인터넷/TCP/IP, SGML(문서 마크업 표준). 발명이라기보다 "조합"이었고, 그래서 이겼다.

### 2.2 핵심 디자인 — 세 가지 발명

버너스리는 1990년 가을, NeXT 컴퓨터 한 대(25MHz 68030, 8MB RAM, NeXTSTEP, Objective-C, Interface Builder)
에서 두 달 만에 아래를 모두 만들었다.

| 발명 | 역할 | 설계 원칙 |
| --- | --- | --- |
| URI/URL | 모든 자원에 전역 이름 | 스킴(`http:`) + 호스트 + 경로. 기존 FTP, Gopher, 뉴스도 담을 수 있게 |
| HTTP | 자원을 가져오는 프로토콜 | 0.9는 `GET /path` 한 줄. 무상태(stateless). 텍스트 기반 |
| HTML | 하이퍼텍스트 문서 포맷 | SGML 문법을 빌리되 극도로 단순하게. 링크(`<a href>`)가 핵심 |

여기에 소프트웨어 두 개:

- WorldWideWeb (1990-12-25 첫 통신 성공): NeXT용 최초의 브라우저이자 편집기. 버너스리의
  웹은 처음부터 읽기/쓰기(read/write)였다. 이 편집 기능이 이후 브라우저에서 사라진 것이
  §5의 "authoring의 귀환" 이야기와 연결된다.
- CERN httpd: 최초의 웹 서버. 첫 사이트 `info.cern.ch`.

1991년 니콜라 펠로우(Nicola Pellow)가 어떤 터미널에서도 도는 Line Mode Browser를 만들어
NeXT가 없는 세상에 웹을 퍼뜨렸다. 1991-08-06 `alt.hypertext` 뉴스그룹에 공개 발표.
1993-04-30 CERN이 웹 소프트웨어를 퍼블릭 도메인으로 공개 — 이 결정이 Gopher(1993년 라이선스료
발표로 몰락)와 운명을 갈랐다.

~~~text
# HTTP/0.9 (1991) — 요청은 한 줄, 응답은 HTML 본문뿐. 헤더도 상태 코드도 없다.
GET /hypertext/WWW/TheProject.html
~~~

~~~html
<!-- 1991년 "HTML Tags" 문서 시절의 HTML. 요소는 18개 남짓. <html>, <body>, <img>가 아직 없다. -->
<TITLE>The World Wide Web project</TITLE>
<H1>World Wide Web</H1>
The WorldWideWeb (W3) is a wide-area
<A NAME=0 HREF="WhatIs.html">hypermedia</A>
information retrieval initiative aiming to give universal access
to a large universe of documents.
~~~

### 2.3 Mosaic — 웹이 대중의 것이 되다 (1993)

- 1992년 ViolaWWW(페이 위안 웨이), Erwise(핀란드), MidasWWW, Lynx(텍스트) 등 X11 브라우저가 등장.
- 1993년 1~4월 NCSA(일리노이대)의 마크 앤드리슨(Marc Andreessen)과 에릭 비나(Eric Bina)가 Mosaic 공개.
  같은 해 11월 Windows·Mac 버전. 성공 요인:
  1. 인라인 이미지 — 앤드리슨이 www-talk에 `<img>` 태그를 제안(1993-02). 버너스리는 문서의 순수성을
     이유로 반대했지만 Mosaic은 그냥 구현했다. "구현이 표준을 이긴다"는 원칙이 처음 드러난 사례.
  2. 설치가 쉬웠고, Windows에서 돌았다.
  3. 클릭 가능한 링크, 뒤로 가기 같은 GUI 관례를 정착시켰다.
- 1994년 앤드리슨과 짐 클라크(SGI 창업자)가 Netscape(초기 이름 Mosaic Communications) 창업. → §4

### 2.4 HTML의 역사

| 버전 | 시기 | 의미 |
| --- | --- | --- |
| "HTML Tags" | 1991 | 버너스리의 비공식 문서. 18개 요소 |
| HTML 1.0 (초안) | 1993 | IETF 초안. 댄 코널리(Dan Connolly) |
| HTML 2.0 | 1995-11, RFC 1866 | 최초의 정식 표준. 폼(`<form>`) 포함 |
| HTML 3.2 | 1997-01 | W3C(1994년 설립) 권고. 넷스케이프·IE가 이미 만든 `<font>`, `<table>`, 애플릿을 사후 승인 |
| HTML 4.0 / 4.01 | 1997-12 / 1999-12 | 스타일과 구조 분리(CSS 전제), 스크립팅, 접근성. 이후 15년간 "표준"으로 군림 |
| XHTML 1.0 | 2000 | HTML을 XML로 재정의. 엄격함이 미덕이라 믿었던 시대 |
| XHTML 2.0 | 2002~2009 (폐기) | 하위 호환을 버린 W3C의 야심. 브라우저 벤더가 거부 |
| WHATWG 설립 | 2004-06 | Apple·Mozilla·Opera가 W3C 워크숍 표결(8:14)에서 패한 뒤 독자 결성. 이안 힉슨(Ian Hickson)의 "Web Applications 1.0" |
| HTML5 | 2014-10-28 W3C 권고 | `<video>`, `<canvas>`, 시맨틱 요소, 파싱 알고리즘 완전 명세(오류 처리까지) |
| HTML Living Standard | 2019-05 이후 유일 | W3C가 WHATWG 문서를 단일 표준으로 인정. 버전 번호 폐기 |

HTML5의 진짜 업적은 새 태그가 아니라 파서 명세다. 그전까지 각 브라우저는 깨진 HTML을 제각각
처리했다. HTML5는 "이 바이트열이 들어오면 정확히 이 트리를 만든다"를 상태 기계로 못 박았고,
그래서 모든 엔진이 같은 DOM을 만든다. §3의 파싱 단계가 바로 이 명세를 따른다.

### 2.5 CSS의 역사

문제: 초기 브라우저는 스타일을 사용자가 정했다(Mosaic 환경설정에서 `<h1>` 폰트를 고르는 식).
넷스케이프가 `<font>`, `<center>` 같은 표현 태그를 넣자 HTML이 오염되기 시작했다.

- 1994-10-10 CERN의 호콘 비움 리(Håkon Wium Lie)가 『Cascading HTML Style Sheets』 제안.
  W3C의 베르트 보스(Bert Bos)가 합류.
- 핵심 디자인: 캐스케이드(cascade). 저자·사용자·브라우저의 스타일이 충돌할 때 우선순위로 합친다.
  이름의 "Cascading"이 바로 이것이다. 그리고 선택자로 문서 구조에 스타일을 매핑한다.
- 경쟁안: 넷스케이프의 JSSS(JavaScript Style Sheets, 1996). `document.tags.H1.color = "blue"` 식.
  Netscape 4에만 실렸다가 사라졌다. 선언적(CSS) vs 명령적(JSSS)의 대결에서 선언적이 이겼다.
- 1996-08 IE 3가 최초로 CSS 일부 지원. 1996-12-17 CSS1 W3C 권고. 1998-05 CSS2. 2011-06 CSS 2.1.
- "CSS3"는 단일 스펙이 아니라 모듈(Selectors, Flexbox, Grid, Transforms...)의 묶음. 이후 버전 번호 대신
  모듈별 레벨.
- 2003년 CSS Zen Garden(데이브 셰이)이 "같은 HTML, 다른 CSS"로 분리의 가치를 대중에게 증명.
- Acid1(1998), Acid2(2005), Acid3(2008) 테스트가 브라우저 간 호환성 경쟁의 상징이 됨.
- 레이아웃 혁명: Flexbox(2009 초안 → 2012 안정), Grid(2017-03 Firefox 52·Chrome 57 동시 출시),
  컨테이너 쿼리(2022~23), CSS 중첩(2023), `:has()`(2022~23).

~~~css
/* CSS1 (1996) 수준의 스타일. 캐스케이드와 선택자라는 뼈대는 30년째 그대로다. */
h1 { font-size: 24pt; color: navy; }
p.note { margin-left: 2em; font-style: italic; }
a:link { color: blue; } a:visited { color: purple; }
~~~

### 2.6 JavaScript의 역사

- 1995-04 브렌던 아이크(Brendan Eich)가 넷스케이프에 입사. 임무는 "브라우저에 Scheme을 넣는 것".
  그러나 Sun과의 제휴로 Java처럼 보여야 한다는 조건이 붙었다.
- 1995-05 10일 만에 프로토타입 Mocha 완성. 9월 Netscape 2.0 베타에 LiveScript로 탑재,
  1995-12-04 Sun과의 공동 발표에서 JavaScript로 개명. Java와는 마케팅 외에 무관하다.
- 설계 DNA: Self의 프로토타입 객체 + Scheme의 일급 함수·클로저 + Java의 문법 + (강요된) 느슨한 타입 변환.
  "디자인 실수"로 꼽히는 `==`, `with`, 자동 세미콜론 삽입은 열흘이라는 일정과 Java 흉내의 대가다.
- 1996-08 마이크로소프트가 역공학으로 JScript(IE 3) 출시. 호환성 지옥이 시작되자 넷스케이프가 표준화를 요청.
  1997-06 ECMA-262 1판(ECMAScript). "JavaScript"는 Sun(현 Oracle)의 상표라 이런 이름이 됐다.

| 판 | 시기 | 의미 |
| --- | --- | --- |
| ES3 | 1999-12 | 정규식, try/catch. 이후 10년간 사실상의 JS |
| ES4 | 2008-07 폐기 | 클래스·타입·패키지를 넣으려던 야심작. Adobe(ActionScript 3)와 Microsoft·Yahoo의 대립. "오슬로 합의"로 폐기 |
| ES5 | 2009-12 | strict mode, JSON, getter/setter. ES4의 잔해에서 건진 것 |
| ES6 / ES2015 | 2015-06 | class, module, arrow, Promise, let/const. 현대 JS의 시작 |
| ES2016~ | 매년 | async/await(2017), optional chaining(2020), top-level await(2022)... |

JS를 "장난감"에서 "플랫폼"으로 바꾼 사건들:

1. DHTML과 DOM(1997~98): IE4/NS4의 각기 다른 DOM → W3C DOM Level 1(1998).
2. XMLHttpRequest: 1998~99 MS Outlook Web Access 팀이 IE5용 ActiveX로 만든 것. 2004년 Gmail, 2005년 Google Maps가
   이 기술로 "페이지 새로고침 없는 앱"이 가능함을 보여 줌. 2005-02 제시 제임스 개럿이 Ajax라 명명.
3. jQuery(2006): 브라우저 차이를 감춘 라이브러리. "JS는 라이브러리로 쓰는 것"이라는 시대.
4. V8(2008-09, Chrome과 함께): JIT 컴파일로 JS를 10배 빠르게. → §3.5
5. Node.js(2009-05, 라이언 달): V8을 브라우저 밖으로. npm(2010)과 함께 JS가 서버·도구 언어가 됨. → §7의 Electron이 여기서 나옴.
6. TypeScript(2012), React(2013), asm.js(2013) → WebAssembly(2017).

~~~javascript
// 1995년 Netscape 2.0 시절의 JavaScript. 폼 검증이 최초의 킬러 유스케이스였다.
function checkForm(form) {
  if (form.email.value.indexOf("@") == -1) {
    alert("Please enter a valid email address.");
    return false;
  }
  return true;
}
// <form onSubmit="return checkForm(this)"> ...
~~~

### 2.7 pros vs cons — 웹의 원초적 설계

| | 장점 | 단점 |
| --- | --- | --- |
| HTML | 관용적 파싱(깨져도 보인다) → 누구나 저자가 됨 | 문서 포맷에 앱을 얹으면서 생긴 왜곡 (`<div>` 수프) |
| HTTP | 무상태·텍스트·캐시 친화 → 무한히 확장 가능 | 세션이 없어 쿠키(1994)로 땜질 → 추적 산업의 씨앗 (§5) |
| URL | 전역 이름공간. 링크 하나로 모든 것을 가리킴 | 링크는 썩는다(link rot). 소유권·영속성 없음 |
| CSS | 선언적. 구조와 표현의 분리 | 전역 범위, 캐스케이드의 예측 불가능성 → BEM, CSS-in-JS, 스코프 CSS의 역사 |
| JS | 배포 없는 실행 환경. 유일한 "모든 곳에서 도는" 언어 | 10일의 부채. 단일 스레드. 타입 없음 → TS의 탄생 |

---

## 3. 브라우저 인터널

> 참고: 이 절은 Chromium 기준이다. WebKit·Gecko도 단계는 같고 이름과 스레드 배치가 다르다.

### 3.1 개요 — 브라우저는 무엇을 하는 프로그램인가

브라우저는 네트워크에서 받은 텍스트를 해석해 픽셀로 바꾸고, 사용자 입력에 반응하며,
신뢰할 수 없는 코드를 안전하게 실행하는 프로그램이다. 마지막 조건 때문에 OS와 닮아간다.

### 3.2 프로세스 모델

Chrome(2008)이 가져온 첫 번째 혁신은 멀티 프로세스다. 탭 하나가 죽어도 브라우저는 산다.

~~~text
┌────────────────────────── Browser Process ──────────────────────────┐
│  UI 스레드(주소창·탭)  ·  네트워크 서비스  ·  저장소  ·  권한 관리  │
└───────────┬──────────────────────┬────────────────────┬─────────────┘
            │ IPC (Mojo)           │                    │
   ┌────────▼────────┐   ┌─────────▼───────┐   ┌────────▼────────┐
   │ Renderer (탭 A) │   │ Renderer (탭 B) │   │   GPU Process   │
   │  Blink + V8     │   │  Blink + V8     │   │ Viz · 합성·래스터 │
   │  샌드박스 안    │   │  샌드박스 안    │   └─────────────────┘
   └─────────────────┘   └─────────────────┘   + 유틸리티/플러그인 프로세스
~~~

- 렌더러 프로세스는 샌드박스에 갇힌다. 파일도 네트워크도 직접 못 만지고 브라우저 프로세스에 부탁한다.
  웹 페이지의 코드는 "신뢰할 수 없는 입력"이기 때문이다.
- 사이트 격리(Site Isolation, Chrome 67, 2018): Spectre 이후 사이트마다 렌더러를 분리.
  `iframe`도 출처가 다르면 다른 프로세스로 간다.
- 대가: 메모리. "크롬이 램을 먹는다"는 이 설계의 비용이다.

### 3.3 내비게이션 — 주소창 입력부터 응답까지

1. UI 스레드가 입력을 판단: 검색어인가 URL인가.
2. 네트워크 서비스가 요청 시작: DNS 조회 → TCP 3-way handshake → TLS handshake → HTTP 요청.
   (HTTP/3 + QUIC이면 UDP 위에서 TLS와 전송을 한 번에.)
3. 응답 헤더 도착: `Content-Type` 확인(없으면 MIME 스니핑), Safe Browsing 검사, 다운로드인지 렌더링인지 판단.
4. 브라우저 프로세스가 렌더러를 준비(실제로는 2단계에서 미리 띄워 둠)하고 커밋(commit): 주소창이 바뀌고 히스토리가 쌓인다.
5. 렌더러가 데이터 스트림을 받아 파싱 시작. `load` 이벤트까지 끝나면 내비게이션 완료.

~~~text
# HTTP 버전별 전송 방식 — 브라우저가 "빠르다"는 말의 상당 부분은 여기서 나온다.
HTTP/1.1 (1997)  텍스트. 연결당 요청 1개 순차 (브라우저는 호스트당 6연결로 우회)
HTTP/2   (2015)  바이너리 프레임. 한 연결에 다중화. 헤더 압축(HPACK). SPDY(Google, 2009)에서 유래
HTTP/3   (2022)  QUIC(UDP) 위. TCP head-of-line blocking 제거, 0-RTT 재연결, 연결 마이그레이션
~~~

### 3.4 렌더링 파이프라인 — 바이트에서 픽셀까지

~~~text
바이트 ─▶ 문자 ─▶ 토큰 ─▶ 노드 ─▶ DOM ──┐
                                        ├─▶ Style ─▶ Layout ─▶ Paint ─▶ Layerize ─▶ Commit
CSS 바이트 ────────────────▶ CSSOM ──┘         (스타일 계산)  (기하)   (그리기 명령)  (레이어)      │
                                                                                                    ▼
                                             ┌─ 컴포지터 스레드 ─────────────────────────────────────┐
                                             │ Tiling ─▶ Raster (Skia, GPU) ─▶ Draw Quads ─▶ Viz ─▶ 화면 │
                                             └───────────────────────────────────────────────────────┘
~~~

#### (1) 파싱 → DOM

- 토크나이저는 HTML 명세의 상태 기계(80여 개 상태). `<`를 만나면 "tag open state"로 가는 식이다.
- 트리 빌더가 토큰을 노드로 만들어 DOM에 삽입. `<p>` 안의 `<div>` 같은 오류도 명세대로 복구.
- `<script>`를 만나면 파싱이 멈춘다(JS가 `document.write`로 DOM을 바꿀 수 있으므로).
  `async`/`defer`/`type=module`이 이를 완화. 그동안 프리로드 스캐너가 앞을 훑어 이미지·CSS·JS를 미리 요청.
- CSS는 렌더 차단: CSSOM이 완성되기 전엔 스타일 계산을 못 하고, JS도 CSSOM을 기다린다(`getComputedStyle` 때문).

#### (2) Style — 계산된 스타일

- 각 DOM 노드에 어떤 CSS 규칙이 맞는지 선택자 매칭(오른쪽에서 왼쪽으로 검사). 캐스케이드·상속·기본값을 합쳐 computed style.
- Firefox의 Stylo(Servo에서 이식, 2017)는 이 단계를 Rust로 병렬화했다.

#### (3) Layout — 기하

- Layout 트리(render tree): `display: none`은 빠지고, `::before` 같은 가상 요소는 들어간다.
- 각 상자의 x, y, width, height 계산. 블록 서식 문맥, 인라인 서식 문맥, Flex, Grid, 테이블, 그리고 텍스트 줄바꿈·글리프 셰이핑(HarfBuzz).
- Chromium은 2019~2021년 레이아웃 엔진을 LayoutNG로 재작성했다. 한국어처럼 단어 경계가 모호한 언어의 줄바꿈·IME 조합 구간 처리는 이 단계와 깊이 얽힌다.
- Reflow: DOM/스타일이 바뀌면 다시 계산. 비싸다. JS에서 `offsetHeight`를 읽으면 브라우저는 강제로 지금 레이아웃을 계산한다(forced synchronous layout).

#### (4) Paint — 그리기 명령 목록

- "무엇을 어떤 순서로 그릴지"(display list / paint records)를 만든다. 아직 픽셀은 없다.
- 순서는 stacking context(`z-index`, `position`, `opacity`, `transform`)가 결정.

#### (5) Layerize → Commit → Composite

- 일부 요소는 독립 레이어로 승격(`transform`, `will-change`, 비디오, 캔버스). 레이어를 따로 래스터해 두면 스크롤·애니메이션에서 다시 그릴 필요가 없다.
- 메인 스레드가 레이어 트리를 컴포지터 스레드에 커밋. 이후 스크롤·`transform` 애니메이션은 메인 스레드가 JS로 바빠도 컴포지터가 혼자 처리한다. 이것이 "부드러운 스크롤"의 정체다.
- 컴포지터가 레이어를 타일로 쪼개 래스터 워커가 Skia로 픽셀 생성(GPU 래스터). 그 결과를 draw quad로 묶어 GPU 프로세스(Viz)에 보내 화면에 합성.

~~~javascript
// 성능의 핵심 규칙 세 가지를 코드로.
// (a) 레이아웃을 읽고 쓰기를 번갈아 하지 마라 — 강제 동기 레이아웃(layout thrashing)
for (const el of items) {
  el.style.width = el.offsetWidth + 10 + "px"; // 읽기(레이아웃 강제) → 쓰기 → 다음 루프에서 또 강제
}
// (b) transform/opacity만 바꾸면 Layout·Paint를 건너뛰고 컴포지터에서 끝난다.
box.style.transform = "translateX(100px)";
// (c) 렌더링 직전에 실행되는 콜백은 requestAnimationFrame.
requestAnimationFrame(() => { /* 다음 프레임 직전 */ });
~~~

### 3.5 JavaScript 엔진 — V8을 예로

~~~text
소스 ─▶ 파서 ─▶ AST ─▶ Ignition(바이트코드 인터프리터)
                          │   실행 횟수·타입 정보(피드백) 수집
                          ▼
                     Sparkplug(2021, 비최적화 베이스라인 컴파일러)
                          ▼
                     Maglev(2023, 중간 단계 최적화)
                          ▼
                     TurboFan(최적화 JIT) ──▶ 가정이 깨지면 Deoptimize ─▶ 다시 Ignition
~~~

- 히든 클래스(shape)와 인라인 캐시: JS 객체는 사전(dictionary)이지만, 같은 순서로 같은 프로퍼티를 넣은
  객체들에 내부적으로 숨은 "클래스"를 부여해 C++ 구조체처럼 오프셋 접근을 한다. 1980년대 Self 언어의 기술이다(브렌던 아이크가 Self에서 프로토타입을 빌려온 것과 같은 계보).
- 가비지 컬렉터 Orinoco: 세대별(young/old), 병렬·동시·증분 수집으로 멈춤 시간 최소화.
- 다른 엔진: JavaScriptCore(WebKit/Safari, "Nitro"), SpiderMonkey(Firefox, 아이크가 만든 원조),
  Chakra(IE/구 Edge, 퇴역), Hermes(React Native), QuickJS(임베디드).

### 3.6 이벤트 루프

JS는 단일 스레드다. 브라우저가 어떻게 "동시에" 네트워크·타이머·렌더링을 처리하는지가 이벤트 루프다.

~~~text
loop:
  1. 태스크 큐에서 태스크 하나 실행 (클릭 핸들러, setTimeout 콜백, 네트워크 응답 처리...)
  2. 마이크로태스크 큐를 비울 때까지 실행 (Promise.then, queueMicrotask, MutationObserver)
  3. 렌더링 기회인가? (보통 60Hz, 디스플레이에 맞춤)
       → requestAnimationFrame 콜백 → Style → Layout → Paint → Commit
  4. 반복
~~~

- 긴 태스크(50ms 초과)가 렌더링 기회를 뺏으면 화면이 멈춘다. Web Worker(2009)가 진짜 스레드를 주지만 DOM은 못 만진다.
- 입력 이벤트는 컴포지터 스레드가 먼저 받는다. `touchstart` 핸들러가 있으면 메인 스레드에 물어봐야 해서 스크롤이 지연되었고, 그래서 `{ passive: true }`가 생겼다.

### 3.7 렌더링 엔진 계보

~~~text
KHTML (KDE, 1998)
  └─▶ WebKit (Apple, 2001 포크 / 2005 오픈소스) ── Safari, (2013까지) Chrome, iOS의 모든 브라우저
        └─▶ Blink (Google, 2013-04 포크) ── Chrome, Edge(2020~), Opera, Brave, Vivaldi, Arc, Dia, Whale, Samsung Internet, Electron, WebView2

Mosaic (NCSA, 1993)
  ├─▶ Netscape (1994) ─▶ Mozilla (1998) ─▶ Gecko ── Firefox, (Quantum 2017: Servo의 Stylo·WebRender 이식)
  └─▶ Spyglass Mosaic ─▶ Trident (IE, 1997~2022) ─▶ EdgeHTML (2015~2020, 폐기)

Presto (Opera, 2003~2013, 폐기)   Servo (Mozilla 2012, Rust → Linux Foundation Europe 2023)
Ladybird (2022~, 독립 신규 엔진, C++ → Swift 전환 중)   Flow (Ekioh, 임베디드)
~~~

2026년 현재 실질적인 범용 엔진은 셋: Blink, WebKit, Gecko. 그리고 Blink가 데스크탑의 대부분을 차지한다. → §4.5

---

## 4. 브라우저의 역사와 브라우저 전쟁

### 4.1 개요

| 시대 | 승자 | 무기 | 교훈 |
| --- | --- | --- | --- |
| 1993~1995 | Mosaic → Netscape | GUI, 인라인 이미지, 속도 | 사용성이 순수성을 이긴다 |
| 1차 전쟁 1995~2001 | Microsoft IE | OS 번들링, 무료, 기업 채널 | 유통이 제품을 이긴다 |
| 암흑기 2001~2006 | IE 6 (95%) | 독점 후 정체 | 독점은 혁신을 멈춘다 |
| 2차 전쟁 2004~2012 | Firefox → Chrome | 표준, 속도(V8), 보안(샌드박스), 단순함, 검색 유통 | 플랫폼 소유자가 결국 이긴다 |
| 크로미움 시대 2013~ | Chromium | 오픈소스 엔진의 중력 | 엔진 다양성의 붕괴 |
| AI 브라우저 2023~ | ? | 에이전트, 컨텍스트 | §6 |

### 4.2 1차 브라우저 전쟁 (1995~2001)

- 1994-12 Netscape Navigator 1.0. 1995-08-09 IPO 첫날 주가 28→75달러. 웹 시대의 개막을 알린 신호탄.
  넷스케이프는 쿠키(1994, 루 몬툴리), JavaScript, SSL, 프레임, `<blink>`를 발명(혹은 강행)했다.
- 1995-05-26 빌 게이츠 메모 『The Internet Tidal Wave』: "인터넷은 1981년 IBM PC 이후 가장 중요한 변화다."
  마이크로소프트는 Spyglass에서 Mosaic 코드를 라이선스해 IE 1.0(1995-08, Windows 95 Plus! 팩)을 급조.
- IE 3(1996-08): CSS, JScript, ActiveX. 처음으로 쓸 만해짐. IE 4(1997-10): Windows에 통합, DHTML.
  출시 파티 뒤 마이크로소프트 직원들이 넷스케이프 잔디밭에 거대한 "e" 로고를 놓고 갔다.
- 무기는 번들링이었다. Windows에 공짜로 딸려 오는 브라우저 vs 49달러짜리 넷스케이프.
  1998-05 미 법무부가 US v. Microsoft 제소. 2000년 1심 "독점 남용" 판결, 분할 명령 → 항소심에서 취소, 2001년 화해.
  브라우저는 이미 IE가 이긴 뒤였다.
- 넷스케이프의 대응: 1998-01 소스 코드 공개(Mozilla). 하지만 Netscape 4 코드가 너무 낡아 엔진을 처음부터 다시 썼고(Gecko),
  그 사이 2년 반의 공백이 치명적이었다. 1998-11 AOL이 42억 달러에 인수. Netscape 6(2000)는 실패작.
- Opera(1995, 노르웨이 텔레노르 사내 프로젝트에서 출발): 유료 시절을 거치며 탭 브라우징 대중화(2000), 마우스 제스처, 스피드 다이얼(2007),
  자체 엔진 Presto. 점유율은 작았지만 UI 혁신의 실험실. CSS의 아버지 호콘 비움 리가 CTO였다.

### 4.3 IE 6의 암흑기 (2001~2006)

- 2001-08 IE 6 출시 후 5년간 메이저 업데이트 없음. IE 팀은 사실상 해산. 점유율 95%.
- 웹 개발자는 IE의 버그가 곧 표준인 세상에 살았다(박스 모델 버그, PNG 투명도, `hasLayout`).
- 한국의 특수성: 1999년 전자서명법 → 공인인증서 → ActiveX 의존. IE가 한국에서 유독 오래 살아남은 이유.
  이는 §7에서 말할 "브라우저에 네이티브 코드를 꽂는" 첫 세대 시도(ActiveX·NPAPI 플러그인·Java 애플릿·Flash)의 폐해이기도 하다.

### 4.4 2차 브라우저 전쟁 (2004~2012)

- Firefox 1.0(2004-11-09): Mozilla 스위트를 경량화한 Phoenix(2002) → Firebird → Firefox.
  탭, 팝업 차단, 확장 기능, 표준 준수. 커뮤니티 모금으로 뉴욕타임스 전면광고. 2009~2010년 점유율 30% 돌파.
  IE를 깨운 것은 Firefox였다. IE 7(2006)이 5년 만에 나온 것은 Firefox 때문이다.
- Safari(2003): 애플이 KHTML을 포크해 WebKit을 만들고 2005년 오픈소스화. 2007년 iPhone과 함께 "진짜 웹을 주머니에".
  iOS는 2024년 EU DMA 전까지 모든 브라우저에 WebKit 사용을 강제했다(App Store 규정 2.5.6).
- Chrome(2008-09-02): 스콧 매클라우드의 만화책으로 발표. 세 가지 무기:
  1. V8 — JS를 컴파일한다. Gmail이 갑자기 빨라졌다.
  2. 멀티 프로세스 + 샌드박스 — 탭이 죽어도 브라우저는 산다.
  3. 단순함 — 옴니박스(주소창=검색창), 탭이 위에, 크롬(UI 테두리)을 최소화. 이름 자체가 아이러니.
  그리고 유통: Google 검색 첫 화면, 안드로이드 기본 탑재, Flash·Java 설치 프로그램에 끼워 넣기, TV 광고.
  6주(2010) → 4주(2021) 고속 릴리스로 "버전"이라는 개념을 없앴다.
- 2011년 말 Chrome이 Firefox 추월, 2012-05 IE 추월(StatCounter). 2차 전쟁 종료.
- 2013-04-03 구글이 WebKit에서 Blink 포크. 같은 해 Opera가 Presto를 버리고 Chromium으로.
- 2015 Microsoft Edge(EdgeHTML). 실패. 2018-12 Chromium 전환 발표, 2020-01-15 Chromium Edge. 2022-06-15 IE 11 지원 종료.
  "브라우저 전쟁의 승자가 패자의 엔진을 채택"한 역사적 순간.

### 4.5 크로미움 시대 (2013~)

| 엔진 | 브라우저 | 비고 |
| --- | --- | --- |
| Blink | Chrome | 점유율 약 65~68% (2025, StatCounter 기준 대략) |
| WebKit | Safari | 약 16~18%. iOS 덕분에 2위. 유일한 비크로미움 상위권 |
| Blink | Edge | 약 5% |
| Gecko | Firefox | 약 2.5~3%. 유일한 독립 오픈소스 엔진. 수입의 80%+가 구글 검색 계약 |
| Blink | Samsung Internet, Opera, Brave, Vivaldi, Whale(네이버), Yandex, Arc, Dia, Comet(Perplexity), Atlas(OpenAI, 2026-08 종료) | 전부 Chromium. 삼성·웨일은 §4.7 |
| Gecko | Zen | Firefox 기반 Arc 스타일 |
| 자체 | Ladybird | 2026년 알파 목표. 앤드리아스 클링(WebKit 출신) |

왜 다들 Chromium인가: 엔진 하나를 유지하려면 해마다 수천 명의 인력과 수억 달러가 든다. 웹 플랫폼 API가 매년 수백 개 늘어난다.
Chromium은 오픈소스이고, 확장 프로그램 생태계(Chrome Web Store)를 그대로 얻는다.
대가: 구글이 웹 표준의 사실상 결정권자가 됐다. Manifest V3(광고 차단 확장 약화, 2019 발표 → 2025 MV2 완전 제거),
Privacy Sandbox, Web Environment Integrity(2023, 반발로 철회) 같은 결정을 다른 브라우저가 "받아쓴다".
2024-08 미 법원이 구글 검색을 독점으로 판결했고 Chrome 매각이 거론됐으나, 2025-09 시정 명령에서는 매각을 요구하지 않았다.

### 4.6 pros vs cons — 단일 엔진 세계

| | 장점 | 단점 |
| --- | --- | --- |
| 개발자 | "Chrome에서 되면 된다". 호환성 비용 급감 | 표준이 곧 Chromium 구현. 비판 없이 API가 굳음 |
| 사용자 | 일관된 경험, 빠른 보안 패치 | 선택권 축소. 광고 회사가 만든 브라우저가 프라이버시 정책을 정함 |
| 생태계 | Electron·Tauri(WebView2)·CEF가 같은 엔진 공유 | 단일 취약점이 전 세계에 동시 노출. Mozilla가 망하면 독립 엔진은 Ladybird뿐 |

### 4.7 한국 시장: 삼성 인터넷과 네이버 웨일

한국은 자국 브라우저 둘이 두 자리 점유율을 갖는 드문 시장이다. 글로벌 구조(Chrome 65 / Safari 18 / Edge 5)와 다르다.

| 한국, StatCounter 2026-08 | 전체 | 모바일 | 데스크탑 |
| --- | --- | --- | --- |
| Chrome | 56.9% | 39.5% | 67.8% |
| Safari | 12.2% | 28.6% | 1.9% |
| Samsung Internet | 10.2% | 13.8% | 8.1% |
| Edge | 9.2% | — | 14.9% |
| Whale | 8.7% | 16.3% | 4.2% |
| Firefox | 1.0% | 0.3% | 1.4% |

모바일 웨일 수치에는 네이버 앱의 인앱 브라우저(웨일 엔진)가 포함되는 것으로 추정된다. 데스크탑 Samsung은 2026년 Windows 버전 출시 이후의 수치다.

#### 삼성 인터넷 → 삼성 브라우저 (2012~)

| 항목 | 내용 |
| --- | --- |
| 역사 | 2012 갤럭시 탑재(초기 WebKit) → 2013 Galaxy S4부터 Chromium → 2015 Play 스토어에 개방 → 2026 v29부터 "삼성 브라우저"로 개명 |
| 규모 | 2016년 자체 발표 MAU 4억. 2026년 글로벌 전체 기기 2.3%(6위), 모바일 2.8%(Chrome·Safari 다음 3위), Android 트래픽의 6.6%(Cloudflare Radar) |
| 기능 | Secret Mode(생체 잠금), 광고차단 확장, 비디오 어시스턴트, 백그라운드 재생, Samsung Pass·Knox·DeX 연동, Galaxy AI Browsing Assist(요약·번역, S24~) |
| PC 진출 | 2025-10-30 Windows 베타(한국·미국) → 2026-03-26 정식. 기기 간 이어하기, Samsung Pass, Perplexity와 공동 개발한 에이전트 어시스턴트(탭 비교·요약, 히스토리 자연어 검색, 영상 구간 점프) |
| 수익 모델 | 브라우저 자체 매출 없음. 구글이 검색·어시스턴트·Play 기본 탑재 대가로 삼성에 4년간 80억 달러(2020~23, Epic v. Google 재판에서 2023-11 공개). 즉 삼성은 Apple처럼 기본값을 파는 쪽이고, 자체 브라우저는 협상 카드이자 생태계 접착제(Chrome에 대한 헤지, Galaxy Store가 Play에 대한 헤지인 것과 같다) |

#### 네이버 웨일 (2017~)

| 항목 | 내용 |
| --- | --- |
| 역사 | 2017 출시(Chromium). 사이드바·옴니태스킹, 세로 탭(2024), 클로바X·ChatGPT·Perplexity·Claude 통합 검색, 멀티플레이(실시간 화면 공유·양방향 조종, 2025-12), 웨일 오토모티브(르노·볼보 차량 탑재) |
| 점유율 | 국내 2024-05 10.5%(최고) → 2025-09 7.23%(4위, 전년 9.47%) → 2025-11 7.88% → 2026-08 8.69% |
| 교육 | 웨일 스페이스(2021-03 출시): 2023-02 기준 17개 시·도교육청, 학교 1.6만 곳, 학생·교사 120만 명. 웨일북(LG전자 공동 개발 교육용 노트북, 예: 전북교육청 1.82만 대), 웨일 UBT(평가 전용 브라우저, NWEC 2025), 웨일 OS Flex |
| B2B | 2023 웨일 엔터프라이즈 베타. 2025-12 "B2B 강화"로 교육·공공·기업 틈새 공략 선언, 2026년 AI 브라우저 출시 목표 |
| 비판 | 2025-11 언론·학계: "외산 AI 서비스를 단순 연동하는 방식으로는 차별화가 어렵다", 클로바X 경쟁력 부족 |

#### 투자자 관점의 해석

- 삼성: 브라우저는 다른 곳(기기)에서 돈을 버는 플랫폼의 부속으로 살아남는다. 글로벌 모바일 3위 브라우저를 아무도 "브라우저 회사"라 부르지 않는 이유. 2026년 Windows 진출과 Perplexity 제휴는 플랫폼 소유자가 AI를 통합하는 시나리오(§6)의 하드웨어 버전이다.
- 웨일: 출발은 Chrome과 같은 논리(네이버 검색 트래픽의 관문을 구글에 맡기지 않는다)였다. 그러나 국내 1위 포털과 자국어 최적화로도 소비자 시장에서 Chrome의 기본값 관성을 넘지 못했고, 교육청 조달이라는 "기관의 기본값"을 사는 쪽으로 선회했다. 업무·교육용 브라우저 분화의 한국 사례이며, 수직 시장에서 조직 단위 B2B로 가는 도구 사업과 같은 모양이다.
- 둘 다 §4.6의 결론을 재확인한다: 소비자 브라우저 점유율은 기본값을 가진 자의 것이고, 도전자의 출구는 인수(TBC) 아니면 부속(삼성) 아니면 기관(웨일)이다.

출처: StatCounter 대한민국(2026-08), 헤럴드경제(2025-11-02), 머니투데이(2025-12-31), 한국경제TV(2023-02-01), 한국경제(2022-09, 2023-12), 삼성 뉴스룸·Samsung Mobile Press(2025-10-30, 2026-03-26), Android Police 해설, Bloomberg·The Hill(Epic v. Google, 2023-11).

---

## 5. 브라우저는 새로운 운영체제다

### 5.1 개요 — 1995년의 예언

앤드리슨이 했다고 전해지는 말: 넷스케이프가 성공하면 Windows는
"제대로 디버깅되지 않은 디바이스 드라이버 묶음"으로 전락한다. 마이크로소프트가 IE에 사활을 건 이유가 이것이다.
당시엔 허풍이었다. 30년이 지나 그 말은 (Windows가 아니라 브라우저 쪽에서) 실현됐다.

운영체제가 하는 일을 브라우저가 어떻게 가져갔는지 보면:

| OS의 역할 | 브라우저의 대응물 | 시기 |
| --- | --- | --- |
| 프로세스 격리 | 렌더러 프로세스 + 샌드박스 + 사이트 격리 | 2008 / 2018 |
| 앱 설치·실행 | URL. 설치가 없다. PWA(2015), 서비스 워커로 오프라인 | 2015~ |
| 파일 시스템 | IndexedDB, OPFS(Origin Private File System), File System Access API(2020) | 2010~ |
| 스레드 | Web Worker(2009), SharedArrayBuffer(2017), Atomics | |
| 네이티브 코드 실행 | asm.js(2013) → WebAssembly(2017) → WASM GC·threads·SIMD | |
| GPU | WebGL(2011) → WebGPU(2023, Chrome 113) | |
| 장치 | 카메라·마이크(getUserMedia), WebRTC(2011), Web Bluetooth/USB/Serial/HID, 위치, 알림 | |
| 앱 스토어 | Chrome 웹 스토어(확장), 그냥 URL | |
| 사용자 계정 | 브라우저 동기화, Passkeys/WebAuthn(2019) | |
| OS 자체 | Chrome OS(2011). 브라우저가 곧 셸인 OS. 미국 K-12 교육 시장 장악 | |

### 5.2 웹 앱의 세 번의 도약

1. Ajax 시대(2004~): Gmail, Google Maps. "페이지"가 "앱"이 되다. Google Docs(2006, Writely 인수)가 오피스를 브라우저로.
   2004년 팀 오라일리의 Web 2.0.
2. HTML5 vs 네이티브(2010~2013): 스티브 잡스의 『Thoughts on Flash』(2010)가 Flash를 죽이고 HTML5를 밀었다.
   그러나 2012년 저커버그가 "HTML5에 건 것이 최대 실수"라며 네이티브로 회귀. 모바일에서는 앱이 이겼다.
3. WebAssembly 시대(2017~): Figma(C++ → WASM, 2016)가 "브라우저에서 도는 전문 도구"를 증명.
   Photoshop 웹(2021), AutoCAD 웹, Google Earth 웹, VS Code 웹(vscode.dev, 2021), StackBlitz WebContainers(Node.js를 브라우저 안에서, 2021).

### 5.3 브라우저가 장악한 것 (1) — 광고

웹의 경제는 광고이고, 광고의 기술적 토대는 브라우저의 설계 결함에서 자랐다.

- 쿠키(1994, 넷스케이프 루 몬툴리): 무상태 HTTP에 장바구니를 붙이려고 만든 것. 명세(RFC 2109, 1997)는
  서드파티 쿠키를 기본 차단하라 권했지만 넷스케이프도 IE도 무시했다. 여기서 크로스 사이트 추적이 태어난다.
- 1996 DoubleClick(광고 서버·추적), 2000 Google AdWords, 2007 구글의 DoubleClick 인수(31억 달러).
  구글 매출의 75% 이상이 광고다. Chrome은 구글 광고 사업의 방어선이다.
- 대응의 역사: AdBlock Plus(2006), uBlock Origin(2014), Safari ITP(2017, 지능형 추적 방지), Firefox ETP(2019),
  Brave(2016, 브렌던 아이크가 창업, 기본 차단 + BAT 토큰).
- 구글의 딜레마: Privacy Sandbox(2019). 서드파티 쿠키 폐지를 2022 → 2023 → 2024로 미루다
  2024-07 폐지 철회, 2025-04 "쿠키 유지" 확정. FLoC → Topics API. 그리고 Manifest V3로 uBlock Origin 무력화(2024~25).
- 핵심 논점: 브라우저를 만드는 회사가 광고로 돈을 벌 때, 브라우저는 누구 편인가. Dia/Arc의 The Browser Company,
  Brave, Vivaldi, Ladybird의 존재 이유가 이 질문에 있다.

### 5.4 브라우저가 장악한 것 (2) — 창작(authoring)

버너스리의 첫 브라우저는 편집기였다. Mosaic·넷스케이프는 그 절반을 버렸고, 웹은 20년간 "읽기 전용"이었다.
쓰기가 돌아온 경로:

| 시기 | 도구 | 의미 |
| --- | --- | --- |
| 1995 | WikiWikiWeb(워드 커닝햄) | 누구나 페이지를 고친다. → 위키백과(2001) |
| 1999~2003 | Blogger, WordPress | 글쓰기가 브라우저로 |
| 2006 | Google Docs | 문서 편집이 브라우저로. 동시 편집(OT) |
| 2012~ | CodePen, JSFiddle → StackBlitz, Replit, Codespaces | 코딩이 브라우저로 |
| 2013 | Canva | 디자인의 대중화 |
| 2016 | Figma, Notion | 전문 디자인·지식 작업이 브라우저로. Figma는 Adobe의 200억 달러 제안(2022, 무산)을 받았다 |
| 2021~ | Clipchamp, CapCut 웹, Photoshop 웹 | 영상·이미지 편집 |
| 2022~ | ChatGPT, Claude, Cursor(VS Code 포크) | AI 창작의 1차 인터페이스가 웹 앱 |

요지: 소비(광고)와 생산(authoring) 양쪽 모두 브라우저 안으로 들어왔다. 그래서 "브라우저에 AI를 넣는다"는 말이
"OS에 AI를 넣는다"와 같은 무게를 갖는다. §6이 여기서 출발한다.

---

## 6. Dia 브라우저

### 6.1 제작자 — The Browser Company of New York

| 항목 | 내용 |
| --- | --- |
| 설립 | 2019, 뉴욕 |
| 공동창업자 | 조시 밀러(Josh Miller, CEO), 허시 아그라왈(Hursh Agrawal, CTO) |
| 투자 | Pace Capital 주도. 총 약 1.28억 달러. 2024년 5,000만 달러 라운드에서 기업가치 5.5억 달러. 엔젤로 LinkedIn 제프 와이너, Figma 딜런 필드, Medium 에브 윌리엄스, Stripe 콜리슨 형제 등 |
| 제품 | Arc(2022~), Arc Search(iOS, 2024), Dia(2025~) |
| 인수 | 2025-09-04 Atlassian이 6.1억 달러(현금)에 인수 발표. 2025-10 완료. 밀러는 잔류. 2026년 "Dia for Companies" 준비 중 |

조시 밀러의 배경:

- 프린스턴 재학 중 중퇴, 2011년 허시 아그라왈·젬레 귄괴르와 Branch(토론 플랫폼)와 Potluck 창업.
  2014-01 페이스북이 약 1,500만 달러에 인수. 페이스북 프로덕트 매니저.
- 2015~2017 백악관 디지털 전략실 제품 디렉터(오바마 행정부). "정부 웹사이트를 사람이 쓸 수 있게".
- Thrive Capital 근무 후 2019년 창업. 창업 에세이의 요지: "우리는 하루 종일 브라우저 안에서 사는데,
  브라우저는 1990년대 이후 바뀌지 않았다. 브라우저는 사실상 OS인데 OS처럼 설계되지 않았다."
- 밀러는 엔지니어라기보다 제품 감각과 글쓰기, 서사에 강한 사람이다. TBC의 특징인 긴 편지, 영상 발표, 디자인 우선 문화가 여기서 온다.
  아그라왈이 기술 축(ADK, Arc Development Kit — Chromium 위에 Swift로 쌓은 자체 프레임워크)을 맡는다.

### 6.2 Dia 이전 — Arc의 성공과 한계

- Arc(2021 비공개 → 2023-07 macOS 공개 → 2024-04 Windows): 사이드바 탭, Spaces(작업 공간), 자동 탭 정리,
  Boosts(사이트 CSS/JS 커스터마이즈), Easels, Little Arc, Split View. Chromium을 Swift(ADK)로 감싸 "맥 앱처럼" 만들었다.
  파워 유저와 디자이너 사이에서 컬트적 인기. 2023-10 Arc Max로 AI 기능(탭 요약, 링크 미리보기, 이름 자동 변경) 시도.
- 2024-10 밀러의 영상 발표: Arc는 기능 완성 상태로 유지보수(보안·버그)만 하고, 회사는 새 브라우저에 집중.
- 2025-05-27 『Letter to Arc members 2025』에서 밝힌 이유:
  1. Arc는 너무 달랐다. "novelty tax(새로움의 세금)" — 배우는 비용이 커서 파워 유저 너머로 퍼지지 못했다.
  2. 사용 데이터: 대부분의 사용자가 Spaces나 Boosts 같은 핵심 기능을 안 썼다.
  3. Arc를 오픈소스로 풀지 못하는 이유는 ADK가 Dia의 핵심 기반이기 때문. 팔지도 않는다.
  4. "AI는 앱이나 버튼이 아니라 브라우저 위에 세워진 새 환경이 될 것". 새 제품은 처음부터 AI를 전제로 다시 짓는다.

### 6.3 Dia — 핵심 디자인

2025-06-11 macOS(Apple Silicon) 베타 공개. Arc 사용자에게 우선 초대. 2025-08 Dia Pro(월 20달러, AI 무제한).

| 디자인 결정 | 내용 | Arc와의 차이 |
| --- | --- | --- |
| 평범한 껍데기 | 가로 탭, 익숙한 Chrome 레이아웃. 확장 프로그램·비밀번호·북마크 가져오기 | Arc의 "다름"을 의도적으로 버림. novelty tax 제거 |
| 주소창 = 채팅창 | URL, 검색, AI 질문을 한 입력창에서. 자연어면 AI, 도메인이면 이동 | Chrome 옴니박스의 다음 단계 |
| 탭이 컨텍스트 | 사이드 채팅에서 `@탭이름`으로 열린 탭을 참조. "이 세 탭을 비교해줘". 복사·붙여넣기 없는 AI | ChatGPT 앱에 URL 붙여넣던 워크플로를 대체 |
| 개인화(메모리) | 브라우징 히스토리를 로컬에 암호화 저장, 질문에 필요한 부분만 서버로. 사용자가 켜고 끔 | "브라우저는 당신에 대해 가장 많이 아는 앱"이라는 논지 |
| 어디서나 쓰기 도우미 | 모든 텍스트 입력창에서 AI로 글쓰기·톤 변경·요약. 페이지 내용을 근거로 | 브라우저 = authoring 표면 (§5.4) |
| Skills | 사용자가 만드는 프롬프트 매크로(`/skill명`). 커뮤니티 갤러리 | Boosts(스타일 커스터마이즈)의 AI 버전 |
| Dia 커서 | 입력 중 문맥 기반 자동완성·제안 | |
| 기반 | Chromium + ADK(Swift). macOS 우선 | Arc와 동일 |

2026년 8월 기준(v1.45) 추가된 것: Profiles(Arc의 Spaces 대체, 별도 창·쿠키·히스토리·메모리 분리), Splits(2분할),
Tab Groups(캘린더 미팅 자동 그룹, Slack·Notion·Confluence 스레드를 따라가는 Live Groups, Live Docs), Focus Mode,
Gmail·Calendar·Slack·Notion·GitHub·Linear·Figma·Outlook 연동, Morning Brief(아침 브리핑), Reports(채팅을 공유 문서로),
E2E 암호화 동기화. 요금제는 Free(Better Browser) / $20 Better Answers / $100 Better Days, 14일 체험.
Windows는 2026년 가을 출시 예정. → 상세는 §6.6

밀러의 논지 요약: "AI를 위한 인터페이스는 새 앱이 아니라 브라우저다. 당신의 일이 이미 거기 있으니까."
탭 = 컨텍스트, 히스토리 = 메모리, 입력창 = 액션. 이것이 "브라우저는 OS"라는 명제를 AI 시대에 맞게 옮긴 것이다.

### 6.4 pros vs cons

#### 장점

- 컨텍스트 무료 획득: 열린 탭과 히스토리가 곧 프롬프트. 별도 RAG 없이 개인화
- 낮은 전환 비용: Chromium 호환, Chrome 확장, 익숙한 UI. Arc의 교훈을 반영
- 프라이버시 포지셔닝: 광고 회사가 아니다. 히스토리 로컬 보관. (구독이 수익 모델)
- 제품·디자인 역량: Arc로 증명된 UI 감각. 빠른 반복
- Atlassian 인수로 자금·엔터프라이즈 유통 확보 (Jira·Confluence 사용자 30만 기업)

#### 단점

- 플랫폼 소유자의 역습: Chrome+Gemini(2025-09), Edge Copilot Mode(2025-07), Safari. 기본 브라우저를 가진 쪽이 유리
- AI 브라우저 난립: Perplexity Comet(2025-07), OpenAI ChatGPT Atlas(2025-10 출시 → 2026-08 종료, ChatGPT 앱·Chrome 확장으로 흡수), Opera Neon, Brave Leo. 차별점이 빠르게 상품화
- 비용 구조: 모든 질문이 LLM 호출. 무료 사용자는 적자. Pro 20달러의 설득력
- 신뢰: Arc를 2년 만에 사실상 중단. "Dia도 그러지 않을까"
- 에이전트 브라우저의 보안: 프롬프트 인젝션(웹 페이지가 AI에게 명령). 2025-08 Brave가 Comet의 취약점을 공개. 브라우저가 "행동"까지 하면 위험도 OS 수준이 된다
- Chromium 종속: 구글의 로드맵(MV3 등)을 따라야 함. 엔진 통제권 없음
- macOS(Apple Silicon, 14+) 전용 출발. Windows는 2026년 가을 예정, 모바일·Linux 없음. 메모리·Skills 체이닝으로 RAM 사용 증가(8GB M1에서 30탭 이상이면 체감)
- 인수 후 "지식 노동자용 업무 브라우저"로 초점 이동 가능성. 소비자 제품과의 긴장

### 6.5 향후 설계에 미친 영향과 현재 상황

- Dia가 제시한 "탭을 @로 참조하는 채팅", "주소창=프롬프트", "히스토리 기반 메모리"는 몇 달 안에
  Chrome(Gemini 사이드 패널, 2025-09), Atlas(사이드 채팅 + 에이전트 모드), Comet(에이전트)에서 유사하게 나타났다.
  Arc의 사이드바 탭이 Edge·Brave·Zen에 복제된 것과 같은 패턴 — TBC는 발명하고 남이 유통한다.
- 논쟁의 축이 이동했다: "AI 기능이 있느냐"에서 "브라우저가 사용자를 대신해 행동해도 되느냐(agentic)"와
  "그 컨텍스트를 누가 보관하느냐"로.
- 남는 질문: Dia는 브라우저인가, 브라우저 모양의 AI 클라이언트인가? 30년 전 넷스케이프가 "브라우저는 OS"라고
  선언했을 때 마이크로소프트가 반응한 것처럼, 지금 구글·OpenAI·Apple이 반응하고 있다.

### 6.6 Dia 일상 사용: 핵심 기능과 필수 단축키

> 2026-09 기준, Dia v1.45. 단축키는 공식 체인지로그에 명시된 것(●)과 리뷰·가이드 한두 곳에만 나온 것(○)을 구분했다.
> 1.3.1(2025-10)부터 모든 단축키를 재설정할 수 있다.

#### 매일 쓰게 되는 기능 10가지

| # | 기능 | 무엇을 하나 | 왜 중요한가 |
| --- | --- | --- | --- |
| 1 | Ask 박스 (주소창 = 검색 = 채팅) | 한 입력창에서 URL 이동, Google 검색, AI 질문. 자연어면 채팅, 도메인이면 이동 | Dia의 정체성. Arc의 커맨드 바(⌘T)를 이것으로 대체했다 |
| 2 | Chat with tabs + `@` 참조 | 사이드 채팅에서 `@탭이름`, `@탭 그룹`, `@프로필`로 범위를 지정. "@A @B 가격과 배송 비교" | 복사·붙여넣기 없는 AI. YouTube·Google Sheets·PDF 첨부도 이해 |
| 3 | Skills (`/`) | 저장된 프롬프트 매크로. 기본 제공: `/Summarize` `/Review` `/Explain` `/PR Description` `/Copy Edit` `/Recap` `/Diagram` `/Table of Contents` … | 반복 작업을 한 단어로. 자연어 스킬 빌더(New Tab → Skills → New Skill)로 "이런 걸 해줘"라고 쓰면 도구까지 골라 준다 |
| 4 | Memory + Personalize | 방문 페이지 요약을 로컬에 저장, `@Search memory 지난주 본 Rust IME 문서`처럼 회수. 학습된 취향(시간대·문체·프로젝트)은 편집 가능 | 기본 꺼짐. 켜면 "브라우저가 나를 아는" 경험이 시작된다 |
| 5 | Profiles | 업무/개인을 별도 창·쿠키·히스토리·확장·메모리로 분리. 탭 바 우클릭 또는 스와이프로 전환 | Arc의 Spaces를 대체. AI 컨텍스트까지 분리되는 점이 Chrome 프로필과 다르다 |
| 6 | Tab Groups · Live Groups | 캘린더 미팅이 열리면 그 시간에 연 탭을 자동으로 그룹화. Slack·Notion·Confluence 스레드, GitHub PR(CI 상태·충돌 미리보기), Live Docs(Notion·Google Docs의 댓글·멘션)를 그룹으로 따라감 | "탭 정리"가 아니라 "일의 단위"가 그룹. 고정·복원·자동 이모지 |
| 7 | Splits · PiP · Focus Mode | 2분할 보기, Google Meet 자동 PiP, ⌘S로 탭 바 숨김 | 회의 중 문서 보기, 집중 글쓰기 |
| 8 | 앱 연동 | Gmail, Google Calendar, Slack, Notion, GitHub, Linear, Figma, Outlook을 채팅 컨텍스트로 | "오늘 Slack에서 나 멘션한 것 정리" 같은 질문이 브라우저 안에서 끝남 (유료) |
| 9 | Morning Brief · Reports · Decks | 아침에 캘린더·받은편지함·Slack을 브리핑. 채팅을 공유 가능한 문서·슬라이드로 | Better Days($100) 전용. "브라우저 = 업무 OS"의 가장 노골적 형태 |
| 10 | 기본 프라이버시 · 동기화 | 광고·트래커 차단 기본 켜짐, E2E 암호화 동기화, Chrome 확장(MV3만) | 광고 회사가 아니라는 포지셔닝의 구현 |

#### 필수 단축키 (macOS)

| 단축키 | 동작 | 출처 |
| --- | --- | --- |
| `⌘T` | 새 탭 = Ask 박스 열기 | ● |
| `⌘L` | 현재 탭에서 Ask 박스 포커스 | ○ |
| `Return` | Ask 박스 기본 동작 (자연어 → 채팅, URL → 이동, 나머지 → 검색) | ○ |
| `⌃⌘Return` / `⇧⌘Return` | 강제로 채팅 / 강제로 Google 검색 | ○ |
| `/` (채팅 입력 중) | Skills 호출 | ● |
| `@` (채팅 입력 중) | 탭·탭 그룹·프로필·메모리 참조 | ● |
| `⌘⇧A` | 탭 검색 (프로필 전체, 모든 창) | ● v1.33 |
| `⌃Tab` | 최근 탭 빠른 전환 (탭 스위처) | ● v1.28/1.33 |
| `⌘S` | Focus Mode — 탭 바 숨김/표시 | ● v1.3.1 |
| `⌘⇧S` | 사이드바 탭 보기 토글 (View → Show Tabs in Sidebar) | ○ |
| `⌥⌘T` | 현재 탭 그룹 안에 새 탭 | ● v1.16 |
| `⌘-클릭` (링크) | 새 탭 그룹으로 열기 / 그룹에 추가 | ● v1.16, 1.36 |
| `⌘⌥S` | Split(2분할) 열기 | ○ |
| `⌘⇧M` | 프로필 전환 | ○ |
| 탭 더블클릭 | 탭 이름 변경 | ● v1.8 |
| `⌘⇧T` · `⌘W` · `⌘1~9` · `⌘,` | 닫은 탭 복구 · 탭 닫기 · n번째 탭 · 설정 (Chromium 표준) | ● |
| `F12` | 개발자 도구 | ● v1.15 |

Arc에서 넘어올 때 부딪히는 것: `⌘T`가 커맨드 팔레트가 아니라 Ask 박스다(Spotlight식 명령 실행이 없다).
Spaces는 Profiles(별도 창)로, 사이드바는 옵션(`⌘⇧S`)으로 바뀌었다. Arc의 `StorableSidebar.json`은 가져올 수 없다.

#### 하루 워크플로 예시

~~~text
09:00  ⌘T → "오늘 일정 정리해줘"            (Calendar·Slack 연동, Better Days면 Morning Brief가 대신함)
10:00  미팅 시작 → 탭 그룹 자동 생성 → 회의 중 연 문서가 한 그룹에
11:00  ⌘⇧A "IME" → 지난주 열었던 WebView2 문서 탭으로 점프
14:00  Rust 문서 3개 열고 사이드 채팅: "@tab1 @tab2 @tab3 차이를 표로"
15:00  PR 페이지에서 /PR Description → 초안 생성 → 입력창에서 톤 수정
16:00  ⌘S 로 탭 바 숨기고 글쓰기, 끝나면 ⌘S
17:00  "@Search memory 오늘 본 Tauri 관련 페이지" → Reports로 묶어 공유
~~~

#### 요금제와 플랫폼 (2026-09)

| 플랜 | 가격 | 포함 |
| --- | --- | --- |
| Better Browser | 무료 | Profiles·탭 그룹·Split·PiP·광고 차단·동기화. AI는 현재 탭 한정 |
| Better Answers | $20/월 | 모든 탭·연동 도구 컨텍스트로 질문, 데이터 미보관 모델. 월 사용량(Tasks) 제한 |
| Better Days | $100/월 | 6배 사용량, Morning Brief, Reports·Decks·Recaps, 미팅 준비·후속 |

Apple Silicon macOS 14+ 전용. Windows는 2026년 가을 예정("Windows Wednesdays" 프리뷰), Intel Mac·Linux·모바일 없음.
새 사용자는 Better Days 14일 무료 체험(카드 불필요).

출처: diabrowser.com 체인지로그(1.2.0, 1.3.1, 1.8.0, 1.10.1, 1.15~1.45), diabrowser.com/plans, SupaSidebar·ComputingForGeeks·XDA 리뷰(2026).

### 6.7 투자는 어떻게 받았고, 브라우저 전쟁 참전은 가치가 있었나

#### 어떻게 투자를 받았나 — 세 개의 지렛대

1. 이미 엑싯한 창업자: 밀러는 2011년 Branch를 에브 윌리엄스(Twitter)·조나 페레티(BuzzFeed)의 시드 약 200만 달러로 시작해 2014년 Facebook에 약 1,500만 달러에 팔았다. 아그라왈이 그 공동창업자다. 이후 백악관 최초의 Director of Product. "검증된 팀"이라는 시드 투자의 첫 조건을 갖췄다
2. VC 안에서 태어난 회사: 백악관에서 형사사법 개혁 프로젝트로 Thrive Capital의 조시 쿠슈너와 알게 됐고, 퇴임 후 Thrive의 EIR → 정식 투자자가 됐다. Thrive 포트폴리오(Airtable, Slack, GitHub, Notion, Figma)를 심사하며 "다들 끝난 줄 알았던 소프트웨어 카테고리를 재발명하는데, 그 앱들이 도는 브라우저만 30년째 그대로다"라는 논지를 세웠다. 회사는 2019-09 Thrive 내부에서 인큐베이션됐고, 밀러는 2020년 코로나 봉쇄 2주 전에 Thrive를 나왔다
3. "브라우저 위에서 회사를 세운 사람들"이 투자자: 시드 500만 달러(2020, Next Play Ventures = 제프 와이너 주도): 딜런 필드(Figma), 악샤이 코타리(Notion), 제이슨 워너(GitHub CTO). 시리즈 A 1,300만 달러(2021-05, Pace Capital 주도): 에릭 유안(Zoom), 패트릭 콜리슨(Stripe), 마이크 크리거(Instagram), 브렛 테일러(Salesforce), 리드 호프먼(LinkedIn), 냇 프리드먼(GitHub). 이들의 제품은 전부 브라우저 안에서 돈다. 브라우저가 나빠지면 자기 제품이 나빠지는 사람들이 브라우저 스타트업에 돈을 냈다

시리즈 B 5,000만 달러(2024-03, Pace Capital 크리스 파이크 주도, 기업가치 5.5억 달러). 누적 약 1.28억 달러.

투자 논지(크리스 파이크, Pace Capital): "브라우저는 운영체제가 되고 모든 소프트웨어는 웹앱으로 접근하게 된다."
따라서 브라우저를 재발명하는 회사는 OS 자리를 노리는 회사다. 근거로 든 것은 Chrome 65%의 독점 아래 30년간 정체된 UX,
그리고 TBC의 주간 배포 속도였다. 수익 모델은 Superhuman식 구독, Perplexity 기본 검색 라이선스(2024-01 제휴), B2B 등을
"나중에 정한다"고 했다. 회사 스스로 "We might not make it"이라는 사이트를 열어 이 불확실성을 인정했다.

#### 가치가 있었나 — pros vs cons

- 재무적으로: 1.28억 달러 투자 → 6.1억 달러 현금 매각(2025-09, 창업 6년). 시드·시리즈 A 투자자는 높은 수익을 거뒀다. 그러나 마지막 라운드(5.5억) 대비 10% 상승에 그쳐, 2024년 시리즈 B는 사실상 본전. VC가 기대하는 10배는 아니다. "실패"가 아니라 "독립 기업으로 끝까지 가지 못한 것"
- 시장 구조로 보면: 넷스케이프 → AOL(1998), Opera → 중국 컨소시엄(2016), Firefox → 구글 검색 계약 의존, TBC → Atlassian. 브라우저 회사는 독립적으로 살아남은 적이 거의 없다. 검색·광고와 짝짓지 않으면 수익이 없고, 검색·광고는 구글과 싸워야 한다. 엔진(Chromium)은 못 바꾸고 셸/UX만 바꾸는 사업은 유통 없이는 파워 유저 너머로 퍼지지 못한다. Arc가 정확히 그 벽에 부딪혔다(novelty tax)
- 전략적으로: 그럼에도 논지는 맞았다. Atlassian이 "직원 업무의 85%가 브라우저에서 일어난다. AI가 브라우저와 업무 계층을 재발명할 것"이라며 6.1억 달러로 그 논지를 샀다. Atlassian 내부에서 이미 수천 명이 Arc를 쓰고 있었다. OpenAI와 Perplexity도 인수를 검토했다
- 카테고리 관점: AI 브라우저라는 카테고리를 TBC가 먼저 정의했고(Arc Max 2023 → Dia 2025), 그 뒤 Perplexity Comet, OpenAI Atlas, Chrome+Gemini, Edge Copilot이 따라왔다. 사이드바 탭이 Edge·Brave·Zen에 복제된 것처럼, 발명은 TBC가 하고 유통은 플랫폼 소유자가 한다
- 창업자 개인으로: 두 번째 엑싯. 밀러는 Atlassian 안에서 Dia를 계속 이끈다. 자금·유통(30만 기업 고객)·보안/컴플라이언스를 얻은 대신 소비자 제품이라는 정체성은 잃었다(Atlassian: "소비자를 겨냥하지 않는다")

#### 결론

투자를 받은 이유는 논지의 참신함보다 사람과 네트워크였다. 이미 한 번 판 창업자, VC 내부 인큐베이션, 그리고 브라우저 위에서
회사를 세운 창업자들의 엔젤 라인업. 논지("브라우저 = OS")는 넷스케이프가 1995년에 한 말과 같고, 그때도 지금도 맞았다.

참전이 가치 있었는지는 "누구에게"에 달렸다. 초기 투자자와 창업자에게는 그렇다. 후기 투자자에게는 간신히 그렇다.
독립 브라우저 회사라는 목표에 비추면 그렇지 않다. 브라우저 전쟁은 1995년 이후 한 번도 스타트업이 이긴 적이 없고,
이번에도 승자는 인수한 쪽과 기본 브라우저를 가진 쪽이다. 이 산업에서 스타트업의 현실적인 출구는
"카테고리를 먼저 정의하고 플랫폼 소유자에게 인수되는 것"이며, TBC는 그 출구를 정확히 통과했다.

출처: Contrary Research 『The Browser Company』 리포트, TechCrunch(2024-03-21, 2014-01-13), 밀러의 X 게시글(2021-05), CNBC·Computerworld(2025-09), Crunchbase/Tracxn.

### 6.8 Next Dia? — Aside (2026)

> 2026-09 기준. 출시 3개월 된 제품이라 지표는 전부 초기값이며, 벤치마크는 자체 보고다.

#### 회사와 팀

- 회사: Aside (YC Fall 2025). 서울 기반 5명. 샌프란시스코 등록
- 창업자: 김준(Jun Kim, CEO) 17세에 Airbridge(AB180, ARR 3,000만 달러) 공동창업 멤버, 솔루션 컨설턴트. 이찬희(Chanhee Lee, CPO, 27) Airbridge 창립 엔지니어, 15세에 MS 후원 비영리 창업. 이상훈(Sanghun Lee, CTO) 14세부터 코딩, 100만 다운로드 앱. 이후 17세 캐나다인을 Discord에서 채용
- 이력: YC에 7번 지원해 F25 합격. 합격 당시 잔고 약 200달러. 이전 피벗 5번. YC 기간 제품은 영업 통화용 실시간 답변 도구(스스로 "Cluely 아류"라 부름)
- 피벗: 2026-01 브라우저로 결정 → 5개월 비공개 개발 → 2026-06-23 출시(macOS). 계기: 투자자들이 "아이디어는 아니지만 제품·UX·엔지니어링은 훌륭하다"고 한 것. 규칙은 "우리가 쓰고 싶은 것을 만든다"
- 초기 지표: 출시 영상 X 조회 160만, 2주 만에 사용자 2만, 첫 달 매출이 이전 제품의 1년 매출 초과. 2026-07-02 업데이트(고정 탭, 세로 탭 사이드바, xAI·Z.ai·로컬 LLM 지원, 비밀번호·지연 버그 수정)
- 요금: Free(월 500 크레딧, 루틴 3개) / Pro $20 / Max $200. 모델은 사용자의 ChatGPT·Claude 구독 또는 API 키(BYO)
- 자금: YC 표준 투자 외 비공개

#### 제품 — 핵심 디자인

- 연동 없는 에이전트: API 연동 대신 브라우저로 로그인해 이메일·대시보드·내부 툴·시트에서 다단계 작업을 끝낸다. 추론 수준 Low/Medium/High/Ultrabrowse(끝날 때까지 자율). 결제·게시·메시지는 승인 게이트
- 로컬 우선 메모리: 기기에 편집 가능한 마크다운 파일로 저장(사람·사이트·날짜별 에피소드). 작업 후 "Dreaming"으로 정리. 작업 전사와 산출물도 로컬 디렉터리
- 에이전트용 비밀번호 관리자: 페이지에 자동입력은 하되 에이전트는 비밀번호를 보지 못한다. 하드웨어 암호화, 작업 단위 권한, 감사 로그
- BYO 모델: 자체 LLM 없음. 사용자의 구독·키를 사용 → Dia의 최대 약점(AI 비용)을 구조적으로 회피
- 벤치마크: Online-Mind2Web 297/300(99.0%). Browser Use 97.7%, GPT-5.4 92.8%, Claude Opus 84.0% 대비 1위 주장. BU Bench v1·Odysseys도 1위 주장. 자체 GitHub 보고, 외부 검증 없음
- 기반: Chromium. 이찬희: "AI로 브라우저를 만들 수는 없다. Chromium은 20년 된 코드베이스다." Arc에 대해: "그들은 사람들이 자기 제품의 무엇을 사랑하는지 몰랐다"

#### pros vs cons — Dia와 비교해서

- Dia보다 나은 점
  - 타이밍: Dia의 층("탭과 채팅", 2025)은 Chrome·ChatGPT에 흡수됐다. Aside는 "일을 끝내는 에이전트"(2026)의 층에 있고, 이 층은 Comet(1,000만 설치)이 살아남은 곳이다
  - 비용 구조: BYO 모델로 LLM 비용을 사용자 구독에 넘겼다. $20 구독이 마진이 된다
  - 가치의 위치: 로컬 메모리 + 자격증명 금고 + 작업 실행기 = AI 브라우저 시대에 가치가 이동하는 곳(컨텍스트·권한·신원). Chromium 껍데기 없이도 존재할 수 있는 자산
  - 자본 효율: 5명·5개월·YC 시드 vs Dia의 100명+·1.28억 달러. 같은 출구라도 배수가 다르다
  - Arc의 교훈("무엇을 사랑하는지")을 읽고 핵심 기능 하나에 집중
- 같은 벽
  - 플랫폼의 역습: Chrome Auto Browse(2026-05), Atlas의 앱 흡수(2026-08). 독립 에이전트 브라우저가 필요한 이유를 계속 증명해야 한다
  - 보안 노출면 최대: 로그인된 사이트에서 자율 실행은 프롬프트 인젝션의 표적. Ultrabrowse의 승인 경계가 개인정보처리방침에 명시되지 않음(eesel 지적). 사고 한 번이 회사를 끝낼 수 있다
  - BYO의 이면: OpenAI·Anthropic 구독 약관에 의존. 서드파티 사용 제한 시 마진 구조 붕괴
  - 지표는 출시 효과: 2만 명은 Arc 초기 팬덤과 같다. Dia의 "핵심 기능 DAU 40%" 같은 침투율이 나와야 비로소 제품이라 할 수 있다
  - 5명이 Chromium 유지: 보안 패치 추적만으로 상당한 인력. Windows 없음. 시드 후 자금 비공개

#### 판단

"인수되는 next Dia"는 충분히 가능하고, TBC보다 빠르고 싸게 그 출구에 닿을 수 있다. "독립 브라우저 회사"로는 §6.7과 같은 구조적 이유로 어렵다.
다른 결말이 있다면 Aside가 브라우저이기를 그만두고 에이전트 런타임(메모리·금고·실행기)이 되는 경우다. OpenAI가 Atlas를 앱으로 접은 것과 같은 방향을,
플랫폼이 아닌 스타트업이 먼저 가는 시나리오.

지켜볼 신호: 에이전트 작업의 주간 리텐션, 보안 사고 유무, Windows 출시, 시드 후 라운드의 리드 투자자, Chrome Auto Browse의 품질.

#### 시드 전 창업자에게

Aside는 §6.7의 반례다. 엑싯 이력도, VC 인큐베이션도, 엔젤 라인업도 없이 YC 7수와 잔고 200달러에서 출발했고, 이력의 빈칸을
제품 자체(벤치마크, 출시 영상, 2주 2만 명, 첫 달 매출)로 채웠다. TBC의 "Being ourselves"와 Aside의 "Build what we want"는 같은 문장이고,
차이는 그 문장을 증명하는 데 든 돈이다. 한국 팀이 샌프란시스코의 AI 프로슈머 시장에서 카테고리를 정의하려 한다는 점도 참고할 만하다.

출처: YC 회사 페이지(Aside, F25), EO Magazine "Aside: The AI Browser Born From the Post-YC Blues"(2026), eesel AI 리뷰·해설(2026-06), Insider Paper(2026-07-02), Tabbit 요금 정리(2026-07-31), Hacker News 출시 스레드(2026-06).

---

## 7. 브라우저의 외적 확장

### 7.1 개요 — 왜 브라우저를 앱에 넣는가

브라우저가 OS라면, 브라우저를 통째로 앱에 넣으면 그 앱은 어디서나 돈다. 데스크탑 앱을 웹 기술(HTML/CSS/JS)로 만들되
파일 시스템·메뉴·트레이 같은 네이티브 기능을 붙이는 것. 이 발상의 계보:

| 시기 | 기술 | 방식 |
| --- | --- | --- |
| 1996~ | ActiveX, NPAPI 플러그인, Java 애플릿, Flash | 브라우저 안에 네이티브 코드를 꽂는다 (반대 방향). 보안 재앙으로 2015~2020년 전부 퇴출 |
| 2008 | CEF(Chromium Embedded Framework, 마셜 그린블랫) | Chromium을 C++ 라이브러리로. Spotify, Steam, Adobe 앱의 UI |
| 2009 | PhoneGap/Cordova | 모바일 WebView에 웹 앱 + 네이티브 브리지 |
| 2011 | node-webkit(NW.js, 인텔 로저 왕) | Node.js + Chromium을 같은 컨텍스트에서 |
| 2013 | Electron(GitHub, 청 자오) | Atom 에디터를 위한 Atom Shell. Node.js + Chromium |
| 2015 | PWA | 브라우저 자체가 앱 셸이 됨. 설치·오프라인·알림 |
| 2019 | Tauri(다니엘 톰슨-이베토, 루카스 노게이라) | Rust + OS 내장 WebView |
| 2019 | Wails(Go), Capacitor(Ionic) | 같은 발상의 다른 언어 |
| 2020 | Microsoft WebView2 | Windows에 Chromium 기반 시스템 WebView 제공. Tauri가 Windows에서 쓸 만해진 계기 |

### 7.2 Electron — 핵심 디자인

~~~text
┌─────────────── Electron 앱 ───────────────┐
│  Main Process (Node.js)                    │  ← 창 생성, 메뉴, 파일, OS API. 앱당 하나
│     │ IPC (ipcMain / ipcRenderer)          │
│  Renderer Process (Chromium)  ×  창 수     │  ← 웹 페이지. contextIsolation으로 Node와 분리
│     └ preload script (contextBridge)       │  ← 렌더러에 안전하게 노출할 API만 골라 줌
│  번들: Chromium 전체 + Node.js 전체        │  ← 이것이 150~200MB의 정체
└────────────────────────────────────────────┘
~~~

~~~javascript
// main.js — Electron의 최소 형태
const { app, BrowserWindow, ipcMain } = require("electron");
const fs = require("node:fs/promises");

app.whenReady().then(() => {
  const win = new BrowserWindow({
    width: 1200, height: 800,
    webPreferences: { preload: `${__dirname}/preload.js`, contextIsolation: true },
  });
  win.loadFile("index.html");
});

ipcMain.handle("read-file", (_e, path) => fs.readFile(path, "utf8"));
~~~

~~~javascript
// preload.js — 렌더러에 노출하는 API를 명시적으로 제한
const { contextBridge, ipcRenderer } = require("electron");
contextBridge.exposeInMainWorld("api", {
  readFile: (path) => ipcRenderer.invoke("read-file", path),
});
~~~

- 채택: VS Code(2015), Slack, Discord, Figma 데스크탑, Notion, Obsidian, 1Password 8, Signal, Postman, (구) Teams.
  "2015년 이후 새 데스크탑 앱의 대부분"이라 해도 과장이 아니다.
- 릴리스는 Chromium을 따라간다(8주). 2019년 OpenJS 재단으로 이관.

### 7.3 Tauri — 핵심 디자인

~~~text
┌─────────────── Tauri 앱 ───────────────┐
│  Core (Rust)                            │  ← 창(tao), 메뉴, 파일, 플러그인, 권한(capabilities)
│     │ IPC: invoke("cmd", args) → Rust    │
│  WebView (OS 내장, wry가 감쌈)           │  ← macOS: WKWebView(WebKit)
│                                         │     Windows: WebView2(Chromium)
│                                         │     Linux: WebKitGTK
│  번들: 앱 코드 + Rust 바이너리만         │  ← 3~10MB. 엔진은 OS가 이미 갖고 있음
└─────────────────────────────────────────┘
~~~

~~~rust
// src-tauri/src/main.rs — Tauri 커맨드. 프론트엔드에서 invoke("read_file", { path })로 호출
#[tauri::command]
fn read_file(path: String) -> Result<String, String> {
    std::fs::read_to_string(&path).map_err(|e| e.to_string())
}

fn main() {
    tauri::Builder::default()
        .invoke_handler(tauri::generate_handler![read_file])
        .run(tauri::generate_context!())
        .expect("error while running tauri application");
}
~~~

~~~typescript
// 프론트엔드 (TypeScript)
import { invoke } from "@tauri-apps/api/core";
const text = await invoke<string>("read_file", { path: "/path/to/note.txt" });
~~~

- 2022-06 Tauri 1.0. 2024-10-02 Tauri 2.0: iOS·Android 지원, 플러그인 시스템, 창·플러그인 단위 capabilities/permissions
  (허용하지 않은 API는 존재하지 않음).
- 자체 엔진 실험: Verso(Servo 기반 WebView, 2024~). "OS WebView 차이"를 근본적으로 없애려는 시도지만 아직 실험 단계.

### 7.4 비교 — pros vs cons

| 기준 | Electron | Tauri | CEF | PWA |
| --- | --- | --- | --- | --- |
| 렌더링 엔진 | 번들 Chromium (단일) | OS WebView (플랫폼별 상이) | 번들 Chromium | 사용자의 브라우저 |
| 배포 크기 | 150~200MB | 3~10MB | 100MB+ | 0 |
| 메모리 기준선 | 100MB+ | 수십 MB | 100MB+ | 브라우저 공유 |
| 백엔드 언어 | Node.js | Rust (또는 사이드카) | C++ | 없음 |
| 렌더링 일관성 | ● 어디서나 동일 | △ Safari(mac) vs Chromium(win) vs WebKitGTK(linux) | ● | ✕ |
| IME·폰트·인쇄 | Chromium 하나만 검증 | 플랫폼별 검증 필요 | | |
| PDF 생성 | Chromium print → PDF 내장 | 직접 구현 또는 사이드카 | 내장 | 브라우저 인쇄 |
| 보안 모델 | contextIsolation, 개발자 책임 | capabilities 기본 거부 | 개발자 책임 | 브라우저 샌드박스 |
| 모바일 | ✕ | ● (2.0) | ✕ | ● |
| 생태계·레퍼런스 | 최대 (VS Code, Slack...) | 성장 중 | 게임·대기업 | |
| 업데이트 부담 | Chromium 보안 패치를 앱이 배포 | OS가 WebView를 패치 | 앱이 배포 | 없음 |

한 줄 요약: Electron은 "일관성을 사고 크기를 지불", Tauri는 "크기를 사고 일관성을 지불".

---

## 8. 연표 한눈에 보기

| 연도 | 인터넷 · 웹 | 브라우저 | 언어 · 표준 | 확장 |
| --- | --- | --- | --- | --- |
| 1969 | ARPANET 첫 메시지, RFC 1, Unix | | | |
| 1974 | TCP 논문 (Cerf·Kahn) | | | |
| 1983 | TCP/IP 전환, DNS, 4.2BSD | | | |
| 1989 | 버너스리 제안서 | | | |
| 1990 | | WorldWideWeb (NeXT) | HTML, HTTP/0.9, URL | |
| 1993 | CERN 퍼블릭 도메인 | Mosaic | `<img>` | |
| 1994 | W3C 설립, 쿠키 | Netscape 1.0 | CSS 제안 | |
| 1995 | 상업 인터넷 개방 | IE 1, Opera | JavaScript, HTML 2.0 | Java 애플릿 |
| 1996 | | IE 3 | CSS1, JScript, HTTP/1.0 | Flash, ActiveX |
| 1997 | | IE 4 | ECMAScript 1, HTML 4, HTTP/1.1 | |
| 1998 | US v. Microsoft | Mozilla 공개 | CSS2, DOM 1 | |
| 2001 | | IE 6 (암흑기 시작) | | |
| 2003 | | Safari (WebKit) | | |
| 2004 | Gmail, Web 2.0 | Firefox 1.0 | WHATWG | |
| 2005 | Google Maps | WebKit 오픈소스 | Ajax | |
| 2007 | iPhone | Mobile Safari | | |
| 2008 | | Chrome, V8 | ES4 폐기 | CEF |
| 2009 | | | ES5, Node.js | |
| 2011 | | Chrome OS | WebGL | NW.js |
| 2012 | Chrome이 IE 추월 | | TypeScript | |
| 2013 | | Blink 포크, Opera→Chromium | React | Electron |
| 2014 | | | HTML5 권고 | |
| 2015 | | Edge (EdgeHTML) | ES2015, HTTP/2 | PWA |
| 2016 | | Brave | | Figma |
| 2017 | | Firefox Quantum | WebAssembly, CSS Grid | |
| 2018 | | Site Isolation | | |
| 2019 | | | HTML Living Standard | Tauri 시작 |
| 2020 | | Edge → Chromium | | WebView2 |
| 2022 | IE 종료 | Arc | HTTP/3 | Tauri 1.0 |
| 2023 | | Arc Max | WebGPU | |
| 2024 | 구글 검색 독점 판결, EU DMA | Arc 개발 중단 | | Tauri 2.0 |
| 2025 | | Dia, Comet, Atlas, Chrome+Gemini | | Atlassian이 TBC 인수 |

---

## 9. 참고 자료

- Tim Berners-Lee, *Information Management: A Proposal* (CERN, 1989); *Weaving the Web* (1999)
- Katie Hafner & Matthew Lyon, *Where Wizards Stay Up Late: The Origins of the Internet* (1996)
- Vint Cerf & Bob Kahn, "A Protocol for Packet Network Intercommunication" (IEEE Trans. Comm., 1974)
- Håkon Wium Lie, *Cascading Style Sheets* (박사 논문, 2005) — CSS의 설계 의도와 경쟁안(JSSS 등)
- Brendan Eich, "A Brief History of JavaScript" (JSConf EU 2010 강연), Allen Wirfs-Brock & Brendan Eich, "JavaScript: The First 20 Years" (HOPL IV, 2020)
- Google, *Inside look at modern web browser* (Mariko Kosaka, 2018, 4부작) — §3의 뼈대
- Tali Garsiel & Paul Irish, *How Browsers Work* (2011)
- Chromium 문서: *Life of a Pixel* (Steve Kobes), *RenderingNG*, *LayoutNG*
- Scott McCloud, *Google Chrome comic* (2008)
- Bill Gates, *The Internet Tidal Wave* (1995 메모, 법무부 공개본)
- The Browser Company: *Letter to Arc members 2025* (2025-05), Dia 공개 글(2025-06), Atlassian 인수 발표(2025-09)
- Electron 문서 *Process Model*; Tauri 문서 *Architecture*, *Capabilities* (v2)
