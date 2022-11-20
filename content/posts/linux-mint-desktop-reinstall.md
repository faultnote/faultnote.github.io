---
title: Linux Mint 다시 설치 기록
tags:
- linux mint
- development environments
description: 시스템 관리 좀 하다가 시나몬 데스크탑 환경이 깨짐. 복구 할 능력이 되지 않아 결국 재설치 하게 되었다.
date: 2017-03-21 07:52:10
readingTime: true
author: soomtong
---

시스템 관리 좀 하다가 시나몬 데스크탑 환경이 깨짐. 복구 할 능력이 되지 않아 결국 재설치 하게 되었다.

## 초기화 후 세팅 가이드

깃헙 같은 원격 레포지토리에 세팅을 저장해 두어도 좋을 것 같다. 필요하면 압축 파일도 함께 저장할 수 있으니 고려해보자.

## 기본 레포지토리 변경

우분투 서버와 민트 서버를 한국 지역으로 변경

> apt 업데이트 후 PPA 는 소스 정보 파일만 복사해서 넣으면 될 줄 알았는데 인증 키 문제로 직접 인증키를 추가하거나 이에 준하는 과정을 거쳐야 했다.

## 쉘 설치: apt 사용해 설치

- fish: 단축키 Shift+ESC 를 사용하는 guake terminal 전용으로 세팅
- zsh: zsh-quickstart-kit (zgen) 을 사용한 환경

> 기존에 사용하던 .zsh(zsh-quickstart-kit) .vim(space-vim) .tmux 등의 설정 파일을 모두 복사해 집어 넣으면 그대로 사용 가능하다. 인증 같은 것들이 필요 없으니 쉘 환경의 사용자 권한만 문제 없다면 어느 환경에서도 사용 가능할 것으로 예상된다.
> Fish 의 설정 작업도 파일위치만 참고해 그 위치에 복사해 두면 이전 세팅 그대로 사용할 수 있다. `fish_config` 명령으로 웹 인터페이스로 세팅할 수 있는 것도 기억해 둘 것
> SSH 관련 작업도 그대로 남아 있다. Git 설정도 마찬가지.

## 글로벌 단축키 변경 및 워크스페이스 세팅

### 마우스 세팅

내 로지텍 G500 을 최대 감도로 사용하기 위한 세팅 과정을 진행한다.

- https://gist.github.com/soomtong/402d5b6c0f56a6c7699a959456626450

> 이 파일을 `/etc/X11/xinit/xinitrc.d/mouse.conf` 로 추가해주고 시나몬 세팅에서 직접 해당 내용을 입력한다. 그냥 파일만 복사해서는 바인딩이 되지 않더라.

### 단축키

dconf-tools 로 dconf Editor 를 설치하면 데스크탑 환경의 각종 세팅을 살펴볼 수 있다. 그리고
dconf-cli 툴을 설치해 `dump`, `load` 할 수 있다.

To export your keyboard shortcut keys, you should do:

```
dconf dump /org/cinnamon/desktop/keybindings/ >keybindings-backup.dconf
```

To later import it (for example) on another computer, do:

```
dconf load /org/cinnamon/desktop/keybindings/ <keybindings-backup.dconf
```

- https://wiki.archlinux.org/index.php/cinnamon

> 이 부분은 새로 지정했다. 입맛에 맞게 글로벌 단축키를 세팅하고 가상 데스크탑은 사용하지 않기로 했다.

> Super+ESC, Super+Tab, Super+` 등의 키가 추가되고 사용에 혼란을 줄 수 있는 키는 제거하였다.

리눅스용 알브레드 앱인 `Synapse` 는 `shutdown` 같은 커맨드도 사용 가능하다.

나는 Alt+Space 로 할당하고 있고 원래 Alt+Space 에 해당하는 윈도우 컨트롤 메뉴는 Super+Space 로 변경해 쓰고 있다.

### 버추얼 콘솔 제거

민트에서 가상 터미널 스위칭 단축키는 CTRL+ALT+F1 ~ 8 까지 있고 F8 은 민트 GUI 환경이다. 그런데, 이거 은근 불편하다. 이 단축키들을 가끔 사용하기 때문이다.

우선 가상 터미널 변경은 단축키로도 작동되지만 내장된 명령어가 있다.

`chvt 번호` 를 통해 변경 가능하다. 시스템에 따라 `sudo` 를 필요로하기도 하다.

```
$ locate xorg.conf
$ pwd
/usr/share/X11/xorg.conf.d
$ sudo vi 100-disable-vt.conf

Section "ServerFlags"
    Option "DontVTSwitch" "true"
EndSection

Section "InputClass"
    Identifier "keyboard defaults"
    MatchIsKeyboard "on"
    Option "XKbOptions" "srvrkeys:none"
EndSection
```
- https://ubuntuforums.org/showthread.php?t=1779702

> 위 가이드 그대로 적용

## 폰트 추가

문서가 가장 잘 되어 있는 듯한 느낌

- https://www.iropke.com/archive/freefont.html

이롭게, 노토산스, 네이버 나눔, 배민 등과 Hack 폰트, 네이버D2Coding 등을 사용한다.

> ~/.local/share/fonts 폴더 복사로 처리 완료 

## 한글 입력기 설치

님프는 예전에 달콤 HWP라이브러리, 다솜입력기로 알려진 김호동 cogniti 님의 작품

fcix 입력기는 이상하게 최신 한글 라이브러리의 내용을 다 불러오지 못하는 듯 그래서 신세벌P2 를 고를 수 없다. 반면 Ibus 는 나온다. 디폴트로 자판 항목이 코딩되어 있을 경우 그런 듯 하다. 세벌님의 레포지토리에 있는 nimf 는 재 컴파일 된 내용 같다.

- https://wiki.ubuntu-kr.org/index.php/Dasom
- http://cogniti-works.blogspot.kr/2016/07/nimf_30.html
- http://cogniti-works.blogspot.kr/2016/05/blog-post.html

세벌식 한글 라이브러리는 여기로: https://launchpad.net/~createsc/+archive/ubuntu/3beol

> createsc/3beol 님의 ppa 를 추가하고 `apt install nimf` 설치로 깔끔히 마무리 된다. 중국/일본 입력기도 설치 되기 때문에 파일 다운로드에 시간이 좀 걸린다. 그리고 새로 컴파일한 libhangul1.so 를 덮어 씌우고 리부팅이 하고

```
im-config
```

로 nimf 를 바인딩 한 후 한 번 더 리부팅 하고 한/영 전환 단축키 (Shift+Space) 세팅으로 마무리한다.

```
$ cd Repository/1-libhangul/hangul/.libs
$ sudo cp libhangul.so* /usr/lib/x86_64-linux-gnu/
```

## SSD  Optimize

구글링 하면 몇 가지 조언이 나오지만 트림 관련 내용만 적용하면 될 듯 하다.

```
$ cat /etc/fstab 
# /etc/fstab: static file system information.
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda1 during installation
UUID=9417bcc8-30e6-47c4-a3b9-f2e16e3bdd1a / ext4 errors=remount-ro,discard 0 1
# swap was on /dev/sda5 during installation
UUID=d6ef61f7-b7d9-4b93-b65e-118b299d435d none swap sw 0 0
```

## VIM env and Tmux env Setup

- https://github.com/johngrib/simple_vim_guide
- https://github.com/liuchengxu/space-vim
- https://github.com/unixorn/zsh-quickstart-kit
- https://github.com/tony/tmux-config
- https://github.com/tmux-plugins/tmux-resurrect

> 닷 파일 그냥 복사해서 처리 완료. 심볼릭 링크가 깨졌을 수 있으니 다시 연결해두는 것으로 해결.

### 기본 에디터 세팅

VSCode 사용시 가끔 `crontab -e` 같은 걸 하게 되면 Code 에디터가 떠서 불편한 경우에는 디폴트 에디터를 변경할 필요가 있다. 이 경우 사용는 커맨드
우분투 기반의 경우에는 이런 커맨드가 제공된다.

```
$ select-editor
```
사용할 에디터로 변경!

## PPA 

한글 라이브러리, 다트, 노드, 엔진엑스 등 프라이빗 레포지토리는 수작업으로 추가

## 애플리케이션들

드랍박스, 캐럿, ~~깃크라켄~~깃지, 심플노트, 한컴뷰어, 슬랙, 시냅스, 코드, 구글 크롬, 오페라 등등

## 기타 유틸리티

yarn, rebase-editor, less, sass(gem), gulp, bower, docker-compose, pm2, node-gyp, hexo, trash, nodemon, asar...

