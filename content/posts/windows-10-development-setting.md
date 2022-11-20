---
title: 윈도우즈 10 개발 환경 꾸미기
tags:
- windows 10
- development environments
description: 2018년 기준 윈도우즈 개발환경 꾸미기
date: 2018-06-28 13:00:47
readingTime: true
author: soomtong
---

> 아마 2013년 말까지 윈도우즈 7 과 리눅스 데스크탑이 설치된 피시를 사용했습니다. 그 이후 아이맥을 제공 받아 매킨토시(OSX) 개발 환경을 유지하고 있다가 2017년 리눅스 기반의 소프트웨어 회사에 들어가면서 어쩌다 윈도우즈 10 을 사용하게 되었는데 그 때 처음 윈도우즈 10 을 사용했던 경험이 함께 기록되어 있습니다.
>
> 윈도우즈 10 개발 환경은 아주 잠깐 사용했고 이내 리눅스 기반 환경으로 돌아온 후 윈도우즈 개발 환경을 꾸밀 일은 없었습니다. 물론 액티브엑스를 반드시 필요로 하는 업무를 치루기 위해서 가상 환경을 통해 윈도우즈 7 을 사용하긴 했으나 네이티브 환경에 윈도우즈를 설치하는 일은 없고 그럴일이 없길 바랬었습니다.
>
> 대부분의 비 윈도우즈 환경 개발자가 그렇듯, 가상환경 속에서 윈도우즈 사용은 그럭저럭 훌륭했고 별다른 불만 없이 이어져 왔으나 가상환경에서 작동하지 않는 프로그램을 사용해야 하는 업무가 생긴 후 네이티브 윈도우즈 피시가 필요하게 되었습니다.
>
> 상대적으로 사용 빈도가 떨어지던 랩탑에 윈도우즈 10 을 설치하고 2017 년의 경험과 최근 윈도우즈 빌드에 맞춘 (내 입맛에 맞는) 세팅 과정을 기록해 둡니다.

## 한성 TFG13 (2017)

설치 대상은 여태 잘 사용하고 있던 리눅스 데스크탑 환경의 랩탑이다. 지금(2018년)은 8세대 인텔 CPU 를 장착해 성능이 더 높아진 TFG13s 모델의 전신으로 2017년 TFG13 이다.

[![리뷰 이미지](/images/windows-10-dev-env/hansung-tfg13s-top100.jpg)](http://www.monsterlabs.co.kr/src/category/read.html?pn=10778)

[해당 업체의 홍보용 리뷰 기사](http://www.monsterlabs.co.kr/src/community/read.html?menu=tbl_comm_review&wr_id=315)

가격 대비 다 마음에 들지만 상대적으로 품질이 떨어지는 패널을 가지고 있다. 처음에는 몰랐는데 장시간 타이핑시 느껴지는 자판의 압력이 불쾌한 제품이다. 동네 전자제품 매장에서 삼성 제품이나 엘지 제품을 타이핑 했을 때 보다 조금 더 압력이 높다. 상대적으로 쫀득하다고 느낄 분도 계실텐데 요즘 나는 가벼운게 좋다.

아무튼 이 작고 가볍고 성능은 고만고만한 랩탑의 활용도가 떨어져 고민하고 있던 차 - 램은 많고 CPU 성능은 떨어진다 - 윈도우즈 10 을 설치하게 되었다. 원래 계획은 여기에 간간히 가상 악기를 사용하는 미디 시퀀싱이나 간단한 강의 영상 편집을 할 생각이었다.

> 전통적으로 영화/영상 제작 분야에서 매우 유명한 AVID 라는 회사가 있는데 이 회사는 최근 경쟁 시대에 살아남기 위해 (예전에는 정말 꿀 빨던 사업이었겠지만) 자사의 핵심 소프트웨어인 오디오/비디오 제작 툴 중 가장 낮은 등급의 버전을 무료로 풀었다. 이미 이 바닥 메이저 툴의 가격이 피튀기는 경쟁으로 각각 몇 십만원 수준으로 내려와 있긴 하지만 무료는 무료 나름대로 큰 의미가 있다. 물론, 아마추어가 쓰기엔 매우 훌륭하고 간단한 작업을 하기엔 충분하다.

그리고 가상 환경에서 작동하지 않는 액티브엑스 기반의 프로그램을 만난 후 이 기회에 본격적인 윈도우즈 환경을 경험해 보자는 결심이 섰다.

하루 밤을 꼬박 새운 결과 하드웨어는 역시 '마이크로소프트' 라는 말이 거짓이 아니었음을 깨달았다! (뭔소린지...)

## 설치 과정

우선 윈도우즈 10 노트북에 포함된 시디키를 사용해 설치를 완료한다. 별도의 하드웨어 드라이버도 필요 없이 끝났다. 예전에는 제조업체 홈페이지에서 드라이버 받아서 설치하고 그랬던 것 같은데 이걸 생각할 겨를도 없이 끝난 것 같다.

MS 계정 연동을 했고, PIN 을 통해 로그인을 진행하도록 설정했다. 스마트폰을 사용하는 느낌이랄까, 나쁘지 않았다.

### 업데이트

1803 (RS4 라고 불리는) 버전을 설치했고 한 3 개 정도 업데이트가 있었다.

그리고 어짜피 윈도우즈 전용 노트북으로 만들 바에 오피스웨어도 제대로 사용해 보자라는 생각에 오피스2016 라이센스도 구입해 설치를 완료했다.

설치하면서 윈도우즈 제공 글로벌 단축키 정도 읽고 있으면 좋을 것 같았다.

- 윈도우즈 10 기본 단축키 https://support.microsoft.com/en-us/help/12445/windows-keyboard-shortcuts

### 내장된 SSH 서비스

이번 설치로 가장 기대되는 것이 있다면 1803 윈도우즈 업데이트에 정식으로 포함된 OpenSSH 서비스이다. 구글링 하면 이전 버전의 내용들이 있어 조금 헷갈리는데 윈도우즈 앱 설정의 선택적 기능 관리에서 OpenSSH 기능을 추가하고 서비스 항목에서 Enable 시키면 별다른 과정 없이 사용할 수 있다. (서비스 항목에서 SSH 로 시작하는 것이 아니라 OpenSSH 로 시작해서 찾기 헷갈렸지만)

![기능 활성화](/images/windows-10-dev-env/open-ssh-packages.png)

![OpenSSH 서비스](/images/windows-10-dev-env/open-ssh-in-windows-service.png)

SSH 키 생성하는 과정 등은 당연히 필요할 것 같고 추가로 설치한 우분투 1804 에서 정상적으로 호스트 윈도우즈 콘솔에 접속이 된다.

호스트 IP 와 WSL 의 IP 는 별도의 대역을 가지고 있으니 각자 확인하면 될 것 같다.

```powershell
C:\Users\soomtong>ipconfig.exe

Windows IP Configuration

(생략)

Wireless LAN adapter Wi-Fi:

   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : *************
   IPv4 Address. . . . . . . . . . . : 192.168.1.119
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 192.168.1.1

(생략)

Ethernet adapter vEthernet (기본 스위치):

   Connection-specific DNS Suffix  . :
   Link-local IPv6 Address . . . . . : *************
   IPv4 Address. . . . . . . . . . . : 172.24.42.193
   Subnet Mask . . . . . . . . . . . : 255.255.255.240
   Default Gateway . . . . . . . . . :
```

아래에 위치한 vEthernet 어댑터가 WSL 의 IP 이다.

```powershell
soomtong@DESKTOP-N3SEVU3 ~> lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 18.04 LTS
Release:        18.04
Codename:       bionic
soomtong@DESKTOP-N3SEVU3 ~> ip addr show up
8: eth2: <BROADCAST,MULTICAST,UP> mtu 1500 group default qlen 1
    link/ether **********
    inet 172.24.42.193/28 brd 172.24.42.207 scope global dynamic
       valid_lft forever preferred_lft forever
    inet6 **********/64 scope global dynamic
       valid_lft forever preferred_lft forever
1: lo: <LOOPBACK,UP> mtu 1500 group default qlen 1
    link/loopback 00:00:00:00:00:00
    inet 127.0.0.1/8 brd 127.255.255.255 scope global dynamic
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope global dynamic
       valid_lft forever preferred_lft forever
18: wifi0: <BROADCAST,MULTICAST,UP> mtu 1500 group default qlen 1
    link/ieee802.11 **********
    inet 192.168.1.119/24 brd 192.168.1.255 scope global dynamic
       valid_lft 5993sec preferred_lft 5993sec
    inet6 **********/64 scope global dynamic
       valid_lft forever preferred_lft forever
```

WSL 에서 확인한 사항들이다. 우분투 1804 를 설치했고 업링크된 IP 를 살펴 보면 윈도우즈 호스트에서 제공하는 IP 와 같다.

```powershell
Welcome to fish, the friendly interactive shell
soomtong@DESKTOP-N3SEVU3 ~> ssh 192.168.1.119
soomtong@192.168.1.119's password:

Microsoft Windows [Version 10.0.17134.112]
(c) 2018 Microsoft Corporation. All rights reserved.

soomtong@DESKTOP-N3SEVU3 C:\Users\soomtong>
```

WSL 에서 호스트 윈도우즈의 OpenSSH 서버에 접속이 완료되었다. 반대의 경우에도 문제 없을 것 같다.

## 개발 관련 필수 소프트웨어

윈도우즈에도 패키지 매니저가 있는건 아는데 이번에도 직접 다운로드해 설치하는 과정을 밟았다. 다음 윈도우즈 환경 세팅에는 도전해 볼 것 같다.

### 에디터

빠르게 스크래치 또는 더미 텍스트 처리 작업을 할 때 필요한 서브라임과 코딩용 메인 에디터로 비주얼스투디오코드를 설치했다.

- 서브라임텍스트 https://www.sublimetext.com/
  - 패키지 매니저 https://packagecontrol.io/
- 비주얼 스투디오 코드 https://code.visualstudio.com/
  - 플러그인 및 단축키 세팅 https://gist.github.com/soomtong/e344dd644f400ebf41d8a5cc1a7082ad

역사와 전통의 메모장은 nodepad2 로 대체했다.

- 노트패드2 http://www.flos-freeware.ch/notepad2.html

### 클라우드 저장소

드랍박스를 오래 사용해왔기 때문에 환경 가리지 않고 드랍박스를 사용한다. 거의 텍스트 데이터 위주기 때문에 기본 제공 플랜으로도 아직 충분한 편이다.

- 드랍박스 https://www.dropbox.com/

### 브라우저

에지 브라우저 좋다. 그래도 메인은 크롬, 그리고 파이어폭스를 추가로 설치했다.

- 크롬 https://www.google.com/chrome/
- 파이어폭스 https://www.mozilla.org/ko/firefox/

### 뷰어

한컴 한글 문서를 보기 위해 한컴 뷰어를 설치한다.

- 한컴 한글 뷰어 http://www.hancom.com/cs_center/csDownload.do

윈도우즈 10 에서 PDF 문서 전용 뷰어를 사용할 필요가 있을까 고민했다. 크롬이나 엣지에서 잘 지원하기 때문이기도 하지만 가벼운 전용 뷰어는 필요하다고 생각된다. 일단 어도비 아크로뱃 리더는 제외하는 편이 좋다. 너무 용량이 크기 때문이기도 하고 그만큼 느리다.

기억을 더듬어보면 오랜동안 Foxit 리더를 사용했는데 예전같지 않더라. 여전히 프리웨어를 유지하고 있지만 용량이 매우 커졌다. 그래서 최근에 사용하는 리더는 SumatraPDF 였다. 윈도우즈 스토어에서 제공하는 어도비 리더 터치도 쓸만 했다.

- SumatraPDF https://www.sumatrapdfreader.org/download-free-pdf-viewer.html
- Adobe Reader Touch

  ![어도비 리더 터치](/images/windows-10-dev-env/adobe-reader-touch-in-store.png)

### 마크다운 에디터

개발자를 위한 글쓰기 도구는 여전히 마크다운이 아닐까 싶다. 맥OS 에는 다양한 글쓰기 도구들이 소개되고 있지만 윈도우즈 환경에는 그렇지 못했던 것 같다. 요즘에는 스크라이브너 같은 전문 툴도 윈도우즈 버전을 제공한다고 하니 윈도우즈 환경이 그만큼 좋아진 것이 아닐까 싶다.

하루패드부터 시작된 인연은 일렉트론 기반의 다양한 마크다운 에디터를 경험하게 해주었고 Abricotine 이나 Texts 같은 앱도 사용해 보긴 했는데 현재 시점에서 멀티 플랫폼을 지원하게 된 Caret 과 Typora 가 가장 추천할 만한 마크다운 에디터가 아닐까 싶다. 타이포라가 윈도우즈를 지원하기 전에는 캐럿을 사용하고 있었는데 이제는 타이포라를 사용하고 있다.

- 타이포라 https://typora.io/
- 캐럿 http://caret.io/

물론 급할 땐 VI 도 쓰고 필요에 따라서 MS VS Code 에서 글을 쓰기도 한다.

### 고정폭 폰트

살면서 만난 빌런 중 가장 즐거웠던 기억을 제공한 '가변폭 코딩 폰트'를 사용하는 현우군을 제외하고 대부분의 개발자는 고정폭 폰트를 코딩용 에디터 폰트로 사용한다. (물론 가변폭 빌런도 지금은 고정폭 폰트를 쓴다고 한다.)

네이버에게 정말 고마워하는 부분이 있다고 너스레를 떤다면 이 미려한 한글이 포함된 D2Coding 을 공개해 준 것이라고 하겠다. 꼭 한 번 사용해 보길!

> 한국 개발자라면 D2Coding 씁시다!

추가로 Fira 를 패치한 Fura 나 우분투 향을 느끼고 싶을 때는 Ubuntu Coding 같은 것도 설치해 사용하지만 D2Coding 이 제일 무난하다.

- 네이버 D2Coding https://github.com/naver/d2codingfont

## 한글 입력

비 주류 세벌식 사용자 중에서도 손에 꼽을 수 있는 팥알님의 신세벌 사용자인 나는 각 운영체제에서 제공하는 기본 입력기로는 한글을 쓸 수 없다. 물론 독수리 타법으로 두벌식 타이핑을 하긴 하지만 그렇게 살면 사는게 사는게 아니겠지.

김용묵님의 날개셋 입력기를 통해 신세벌 자판을 사용한다.

- 날개셋 http://moogi.new21.org/ngs_download.htm

그동안 팥알님의 신세벌 P2 자판을 사용하다 몇 가지 더 수정한 버전을 사용하고 있다.

- 팥알님의 신세벌 P2 배열 https://pat.im/1136
- 개인적으로 수정해 사용하는 배열

![신세벌 수정 배열](/images/windows-10-dev-env/shin3-layout-DgY8N3sUcAAid5P.jpg)

팥알님께서 최근 기호 배열을 수정하신걸 이제 알았다. 홑따옴표 자리는 나와 마음이 통했나보다.

## 터미널

윈도우즈 10 의 터미널 환경은 지난 윈도우즈 7 의 그것과 모양은 비슷하지만 기능은 더 개선된 것 같다. 다양한 고정폭 폰트 사용할 수 있게 되었다. 게다가 파워쉘을 통해 더 나은 터미널 환경을 제공하고 있기에 매우 환영할 만 하지만 그래도 여전히 구린 것은 왜일까.

아무튼 윈도우즈 서브 시스템 리눅스가 힙한 개발자의 핫한 개발 환경이기에 반드시 쓸만한 터미널 유틸리티를 설치해야 한다.

이건 기본 터미널을 제공하는 맥OS 나 리눅스 DE 에서 추가로 터미널 앱을 설치하는 것과 마찬가지라고 볼 수 있다.

- iTerm2 https://www.iterm2.com/
- Guake Terminal https://github.com/Guake/guake

윈도우즈 환경에서 골라낸 쓸만한 터미널 애플리케이션은 Fluent Terminal 과 Cmder 로 오픈소스로 관리되고 있고 ConEmu 라는 콘솔 앱에 커스텀 UX 를 더한 것으로 위에 거론한 iTerm 이나 Guake 에 버금가는 사용성을 제공하고 있다. 지금은 단순하지만 살짝 더 윈도우즈 네이티브한 UI 를 제공하는 Fluent 터미널을 사용하고 있다.

- Fluent Terminal https://github.com/felixse/FluentTerminal
- Cmder http://cmder.net/
- ConEmu https://conemu.github.io/

내장 터미널과 파워쉘 및 WSL 의 콘솔을 대체하는 기능까지 포함되어 있다. 이제 ssh 까지 내장되어 있으니 쓸만한 터미널만 있으면 세상 평화인 분들에게는 정말 기쁜 업데이트가 아닐 수 없겠다.

![Cmder](/images/windows-10-dev-env/cmder-sample.png)

다음 링크는 Cmder 에 대한 다양한 팁과 한글 출력과 관계된 문제를 해결하는 방법이 기록되어 있다.

- https://www.lesstif.com/pages/viewpage.action?pageId=51282114
- http://webdir.tistory.com/548

## 유틸리티

왠만하면 오랜동안 손에 익은 툴이 있으면 좋다고 생각한다. 윈도우즈든 맥이든 리눅스든 필수 요소들이 있다.

### 퀵 룩

맥OS 의 파인더나 리눅스 시나몬 DE 의 Nemo 파일매니저에는 퀵-룩 이라 불리는 기능이 기본 제공된다. 운영체제에서 해석할 수 있는 파일에 대하여 해당 응용 프로그램을 통해 파일을 열지 않아도 간략하게 프리뷰를 해 주는 기능인데 보통 이미지 파일이나 오디오 파일에 스페이스 바를 눌러 확인할 수 있다.

윈도우즈에도 이런 유틸리티가 있다. 전통적으로 Seer 라는 앱이 유명했고 성숙기를 접어들어 유료 모델을 도입해 제공하고 있으며 새로운 오픈소스 제품으로 QuickLook 이라는 앱이 괜찮은 것 같다. 둘 다 매우 좋은 성능을 보여 주고 있어 어느 것을 사용해도 괜찮지만 오픈소스 버전을 선택해 설치했다.

- 퀵룩 https://pooi.moe/QuickLook/
- 씨어 http://1218.io/

### 스폿 라이트

또 빠질 수 없는 유틸리티를 꼽자면 애플리케이션 런처 계열 중 하나인 알프레드 스타일의 런처인데 맥OS 에 내장된 스폿라이트나 리눅스 버전에서 제공하는 시냅스 애플리케이션이 이에 해당된다.

윈도우즈에서도 이런 류의 애플리케이션이 존재하는데 국내 제품으로 하인이라 불리는 앱을 사용했었다. 하지만 이번에 선택한 앱은 Wox 라 불리는 제품이다. 역시 오픈소스 버전을 제공하고 있다.

![웍스](/images/windows-10-dev-env/wox-sh-down.png)

윈도우즈 시작 메뉴가 워낙 강력해 별도의 런처 앱을 설치할 이유가 없어 보이긴 하지만 습관적으로 사용하던 단축키에 해당 기능이 없을 때 느끼는 아쉬움을 채워주기에 충분히 훌륭하다. 단축키를 변경하는 것은 물론이고 시스템을 종료하는 기능 등 다양한 방법으로 사용 편의성을 높여 준다.

- 웍스 http://www.wox.one/

더불어 파일 검색에 최적화 되어 있는 Everything 도 설치해 사용하고 있다.

### 스크린 캡쳐

화면을 캡쳐 하는 작업은 어느 시스템이나 기본적으로 제공하는 기능이지만 별도의 유틸리티가 필요할 때가 있다. 내 경우 이전 위치를 그대로 다시 캡쳐 해 주는 기능 때문에 이 기능이 제공되는 화면 캡쳐 프로그램을 골라쓰는 편이다.

반디집 개발자님께서 제공해 주시는 반디캠도 훌륭하지만 또 다른 국내 개발자님이 제공해 주시는 픽픽 이란 툴을 추천한다.

- 반디집 https://www.bandisoft.com/bandizip/

이전 위치 다시 캡쳐 기능이 제공되고 간단하지만 왠만한 기능은 다 갖춘 이미지 편집 툴도 함께 제공된다. 풍부한 기능과 사용성은 물론이고 사용자 인터페이스가 오피스 앱 수준으로 멋있어 쓸 때마다 만족도가 높다.

- 픽픽 https://picpick.app/ko/

### 키보드 매핑

독특한 한글 자판 배열을 사용하고 있기도 하지만 윈도우즈에서도 캡스락 키를 한/영 전환 키로 사용하기 위한 노력을 계속하고 있었다. 가상 환경에서는 별도의 유틸리티가 필요 없었다. VM 에서 제공하는 키매핑 메뉴를 사용하면 충분했다.

실제 환경에서도 다양한 유틸리티를 통해 시스템을 트윅할 수 있는데 가장 좋은 솔루션은 SharpKeys 라는 앱을 사용하는 것이다.

직관적으로 키를 매핑해 준다. 단 1:1 매핑만 가능하다. 이 정도면 충분히 한/영 키와 캡스락 키를 교환해 사용할 수 있다.

- 샵키 http://www.randyrants.com/category/sharpkeys/

### 마우스 콘트롤

마우스 휠 대신 오른쪽 버튼 드래그로 화면 스크롤을 시키는 역할을 하는 툴이 있다. 맥에서는 `Smart Scroll` 이 이 기능을 담당하고 있고 윈도우즈에는  `MouseImp` 라는 유틸리티가 이 기능을 제공했는데 이제는 개발이 중단된 상태이다.

- 스마트 스크롤 http://www.marcmoini.com/sx_en.html
- 마우스 임프 http://www.mouseimp.com/

앱 사이닝도 되어 있지 않고 그래서 부팅 후 자동 실행도 되지 않아 이번 설치에서 배제했다.

## 프로그래밍 언어 관련

노드빠인 관계로 당연히 노드JS 를 설치했다. 간간히 네이티브 모듈 빌드를 위해 파이썬과 네이티브 컴파일 툴체인이 필요한데 적당히 패키징한 노드 패키지가 있다.

예전에는 npm-windows-upgrade 라는 이름으로 제공되었는데 최근에는 명시적인 이름으로 바뀐 것 같다. 파이썬 2.7 도 함께 제공된다.

- windows-build-tools https://www.npmjs.com/package/windows-build-tools

```powershell
C:\Users\soomtong>npm info windows-build-tools

windows-build-tools@3.0.1 | MIT | deps: 6 | versions: 51
Install C++ Build Tools for Windows using npm
https://github.com/felixrieseberg/windows-build-tools#readme

keywords: Windows, Build Tools, node-gyp, native, c++

dist
.tarball https://registry.npmjs.org/windows-build-tools/-/windows-build-tools-3.0.1.tgz
.shasum: 8d56aacb9039f50104850c0d82be529db34e9696
.integrity: sha512-vLkLYldNB5q1MgsWQhEVWfQjp6C9JyH7bEk0+y1Bv5DGUQR2ON7hDcbgDo/pKlbNLTf2qIgquYE64lCmWJsstQ==
.unpackedSize: 110.4 kB

dependencies:
chalk: ^2.4.1        fs-extra: ^5.0.0     nugget: ^2.0.1
debug: ^3.1.0        in-gfw: ^1.1.2       string-width: ^2.1.1

maintainers:
- felixrieseberg <felix@felixrieseberg.com>

dist-tags:
beta: 3.0.0-beta.3  latest: 3.0.1

published 6 days ago by felixrieseberg <felix@felixrieseberg.com>
```

깃 버전 관리 시스템 역시 직접 설치한다.

- git-scm https://git-scm.com/downloads

GUI 클라이언트로는 Fork 를 사용한다.

- Fork https://git-fork.com/

### 러스트

고맙게도 rust-init 이 제공된다. 이거 설치하고 rustup 으로 세팅하면 끝난다. 정말 깔끔하다. 처음에 러스트를 구경할 때는 C/C++ 개발자들이 다 러스트로 이동할 것 같았지만 지금 보면 웹 개발자들이 더 많이 러스트를 파고 있는 것 같다.

- 러스트업 https://rustup.rs/

## 소스 디렉토리

오랜동안 리눅스와 맥OS 에서 홈 폴더 아래에 Repository 라는 폴더를 코드 저장소로 사용했는데 윈도우즈에서는 내 문서 안으로 저장소 폴더를 옮겼다. 딱히 이유는 없고 거기가 더 안락해 보였다고나할까...

그래서 에디터나 터미널에서 초기 디렉토리를 이곳으로 지정해 두었다.

이에 해당되는 VS Code 의 세팅은 아래와 같다.

```powershell
"terminal.integrated.cwd": "C:/Users/soomtong/Documents/Repository",
"terminal.integrated.shell.windows": "C:\\Windows\\System32\\bash.exe"
```

## 기타

그외 예전 작업 기록으로 Cygwin 세팅 같은 것들이 남아 있어 기록해 둔다.

### Cygwin 세팅

1. 쉘 세팅
   폰트를 D2Coding 으로 변경, 디폴트 사이즈 변경, 터미널 타입 변경(xterm-256color)
2. 기본 쉘 변경
   /usr/bin/fish 로 변경. 시그윈에는 chsh 가 없다. 그래서 /etc/nsswitch.conf 에서 기본 쉘을 변경할 수 있다. 참고 [링크1](http://superuser.com/questions/940529/how-to-change-the-default-shell-in-cygwin), [링크2](http://stackoverflow.com/questions/22363210/set-default-shell-in-cygwin/37815158)
3. 워킹 디렉토리 마운트
   심볼릭 링크를 사용해 시그윈 루트에 작업 폴더를 달아둔다. 마운트된 디렉토리는 mount 명령을 통해 확인 가능하다. `ln -s /cygdrive/c/Users/soomtong/Documents/Repository ~/.`
4. 추가 패키지 설치
   다운로드했던 setup-x86_64.exe(32bit 버전은 그에 해당하는 것으로) 다시 실행해 설치하는 과정을 겪으면 된다. 카테고리별 구성도 확인할 수 있다. 또는 해당 명령에 옵션을 주어 필요한 만큼 설치할 수 있다. 근데 어려워 보임. [링크](http://www.cygwin.com/faq/faq.html#faq.setup.cli)
5. 터미널 컬러 오류
   `set -gx LS_COLORS (echo (dircolors -c $colorfile | string split ' ')[3] |string split "'")[2]` 를 `./config/fish.config` 안에 넣어둔다.

### 구라제거기

말이 필요 없다. 보안을 위한 키로거 등을 제거해 준다.
