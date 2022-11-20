---
title: 리눅스 민트에서 가상 터미널 사용하지 않기
cover: '/images/mint-beta.jpeg'
date: 2018-10-29 15:29:21
tags: 
- development environments
description: 민트에서 가상 터미널 스위칭 단축키는 CTRL+ALT+F1 ~ 7 까지 있고 F7 은 민트 GUI 환경이다. 그런데, 이게 매우 유용할 때도 있지만 은근 불편하다. GUI 기반으로 환경 세팅을 빡세게 한다면 이 단축키들을 가끔 사용하기 때문이다.
readingTime: true
author: soomtong
---

> 본 내용은 아래 링크를 참조했습니다.
> https://ubuntuforums.org/showthread.php?t=1779702

## 가상 터미널

민트에서 가상 터미널 스위칭 단축키는 CTRL+ALT+F1 ~ 7 까지 있고 F7 은 민트 GUI 환경이다.
그런데, 이게 매우 유용할 때도 있지만 은근 불편하다.
GUI 기반으로 환경 세팅을 빡세게 한다면 이 단축키들을 가끔 사용하기 때문이다.

![GUI 데스크탑 환경에서도 빠르게 터미널에 연결해주는 가상 터미널](/images/linux-virtual-terminal.gif)

우선 가상 터미널 변경은 단축키로도 작동되지만 내장된 명령어가 있다.
`chvt 번호` 를 통해 변경 가능하다. 시스템에 따라 `sudo` 를 필요로하기도 하다.

### disable-vt.conf 를 만들어 보자

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

## 리눅스 민트 데비안 에디션

LMDE 3 Cindy 의 경우 리눅스 커널 4.9 를 기반으로 하고 있다. 적절하다. 너무 낮지도 않고 너무 높지도 않다.
시나몬 환경은 아주 익숙하고 미려하다. KDE 쪽이나 다른 Gnome 류들 보다 맘에 든다. 아마 새로운 데스크탑을 세팅한다면 LMDE 기반으로 꾸밀 것 같다.
