+++
title = "2022년에 Doom Emacs 시작하기"
slug = "doom-emacs-intro"
date = "2022-01-01T14:10:21+09:00"
author = "soomtong"
cover = "/posts/images/cover-silliconvally-vim-over-emacs.jpeg"
tags = ["editor", "emacs"]
keywords = ["doom", "emacs"]
description = "2022년에 시작하는 이맥스로 글쓰기 환경 구축"
showFullContent = false
readingTime = true
+++

## 이맥스는 뭐냐?

오리지널 Vi 보다는 늦지만 Vim 보다 조금 먼저 개발된 텍스트 편집기로 오래되었고, 요즘은 VSCode 가 승리했지만 Emacs vs Vim  성전(聖戰, a holy war)의 핫한 주인공이었다. (지금 저 `vs` 는 VSCode 가 되었고 코딩용 편집기의 de facto 가 되었다.)

> [I do use Vim other Emacs](https://www.youtube.com/watch?v=3r1z5NDXU3s) 커버에 있는 이미지의 클립에서도 볼 수 있듯 연애 보다 중요한 문제다.

### Vi 와 Vim

Vim 은 Vi Improved, 즉, Vi 의 개선판으로 퍼블릭 버전은 1991년에 등장했다.

오리지널 vi 는 [빌 조이](https://en.wikipedia.org/wiki/Bill_Joy)가 만들었고 대부분의 UNIX 시스템의 에디터로 채용되었다. 이후 [브람 뮬러너](https://en.wikipedia.org/wiki/Bram_Moolenaar)가 Vi Imitation 으로 시작해 Vim 은 지금까지 가장 유명한 에디터 중 하나로 추앙 받고 있다.

> Vi 와 Vim 에 대한 조금 더 자세한 얘기는 다른 포스트에서 다뤄보기로 하자. 

[The History of Vim](https://jovicailic.org/2014/06/the-history-of-vim/) 문서에 따르면 이런 히스토리를 가지고 있다.

- 1988 Vim 1.0 Vi IMitation on the Amiga
- 1991 Vim 1.14 First public release (on Fred Fish disk #591)
- 1992 Vim 1.22 Port to Unix, renamed to Vi IMproved
- 1994 Vim 3.0 Multiple windows
- 1996 Vim 4.0 GUI
- 1998 Vim 5.0 Syntax highlighting
- 2001 Vim 6.0 Folding, multi-language
- 2006 Vim 7.0 On the fly spell checker, support for tabs
- 2017 — Mastering Vim Quickly book published.

현재는 8.x 버전이 사용되고 있고 Vim 의 모던 버전인 [Neovim](https://neovim.io) 과 포스트 모던 버전인 [Helix](https://helix-editor.com) 에디터가 떠오르고 있다.
 
### Emacs

이맥스는 GNU 의 역사와 괘를 같이 한다. GNU 최초의 프로젝트로 알려져 있기도 하다.

> 에디터와 컴파일러, 디버거 개발로 시작된 상용 UNIX 시스템을 대체하는 GNU 프로젝트는 커널 개발이 늦어져 그 자리를 Linux 로 채우게 되었다. 오리지널 GNU 명맥을 유지하는 커널은 [GNU/Hurd](https://www.gnu.org/software/hurd/) 로 계속되고 있다.

GNU 프로젝트의 구루인 RMS - [리처드 스톨만](https://en.wikipedia.org/wiki/Richard_Stallman "GNU 의 창시자")이 lisp 프로그래밍 언어로 개발했다. Java 를 개발한 [제임스 고슬링](https://en.wikipedia.org/wiki/Gosling_Emacs "자바의 아버지")도 이 에디터의 상용 버전을 개발했던 기록이 있다.

> [Writing an Emacs implementation in C (Gosling Emacs)](https://www.youtube.com/watch?v=wA7aB-oxjVc) 이 영상 재밌다. Latax 보다 worse 한 이맥스의 전신에 대한 얘기도 나온다.

X-윈도우 시스템을 위한 이맥스가 개발되었고 Vi/Vim 와 다르게 자체 GUI 애플리케이션으로 배포되기도 한다. 스크립트를 통한 확장이 용이하여 다양한 기능이 추가되었고 수많은 해커들의 사랑을 받아 이맥스 안에서 모든 것을 해결할 수 있을 수준의 환경을 구성할 수 있다. 반면, 속도의 향상을 위해 C 네이티브로 구성된 부분이 있지만 elisp (Emacs Lisp) 인터프리터 위에서 수행되는 관계로 상대적으로 무겁다. 

긴 역사를 넘어 그 보다 더 많은 개발자들의 공헌으로 다양한 응용프로그램을 구동할 수 있는 환경을 갖추게 되었고 현대의 IDE 에 버금가는 기능과 그 이상의 시스템으로 발전해왔다.

이맥스의 단축키는 다양한 시스템에 영향을 주게 되었고 대부분의 쉘에서 이 단축키 일부를 지원하고 있다.

본격적인 문서는 [EmacsWiki 한글](https://www.emacswiki.org/emacs?interface=ko "이맥스 위키") 사이트를 참고하자.

#### Evil 모드

이맥스의 단축키는 CTRL 키와 META 키 - 윈도우즈의 ALT 나 맥OS 의 Option 키 - 그리고 SUPER 키를 지원하고 이를 응용할 수 있다. 두 단계 이상으로 키 조합을 구성할 수 있어 왠만한 기능은 키보드로 처리가 가능하다.

하지만 모달 에디팅 환경을 제공하는 Vim 과 다르게 기본적으로 일반적인 에디터가 제공하는 인라인 편집 모드를 제공하고 있다.

> 모달 에디팅에 대해서는 다음에 심각하게 다뤄보도록 하겠습니다.

모달 에디팅의 장점은 다음에 알아보도록 하고 현재, Vim 에 익숙한 사용자들은 이맥의 기본 편집 모드를 바로 사용하는게 불편했다.

이런 문제는 이맥스의 다양한 확장 기능 중 Evil 모드 시스템을 통해 Vim 과 동일한 모달 에디팅 환경을 제공 받는다.

Vim 과 같은 모달 에디팅 환경이 제공되는 Emacs 는 Vim 을 넘어서는 텍스트 편집 환경을 제공하는 장점이 있어 Vim 사용자들을 Emacs 사용자로 만드는 중요한 역할을 했다.

## 이맥스에 대한 개인 취향

나의 이맥스 사용은 주로 적당한 분량의 문서 작성에 사용하고 있다.[^1]

밥벌이 코딩은 `Jetbrains IDE` 에서 하고 짧은 메모나 임시 코드 작성 보관은 아무 에디터에서 진행하고 있는데 대표적으로 `Sublime Text` 에서 수행한다. 특히 포매팅 작업이 필요한 코드를 잠시 보관할 때는 이만한 도구가 없다.

1. 매우 빠르고
2. 스크래치 파일을 잘 관리해주고
2. 하지만, 빈티지 모드가 영 시원찮고
3. 구름입력기의 `Esc` 키로 로마자 전환 기능을 사용하는데 자동 영문 전환이 매끄럽지 않다.

어쨋든 긴 글은 이맥스에서 작성하려고 시도하고 있고 - 물론 쌩짜 이맥스를 사용하진 않는다 - 스페이스맥스에서 둠 이맥스까지 오게 되었는데 이 오래된 툴은 확실히 묘한 매력이 있다.

결국 중요한 것은 적당히 굼뜨지 않고 글쓰기를 위한 물리적 감성적 공간을 충분히 제공해주며

1. 한글 입력에 문제가 없어야 하고
2. 입력 모드에서 일반 모드로 전환시 로마자 입력 상태로 전환이 잘 되어야 하며
3. 마크다운 편집이나 스니핏 코드 하이라이트 정도는 되면 좋고 
4. 화살표 키가 없는 키보드를 사용하는 나에겐 적당한 모달 모드를 지원해야 한다. 

여기에 org 모드는 이맥스를 사랑하게 될 것 같은 예상이 든다. (아직 깊이 빠지진 못했다.)

### 사용 중인 커스텀

한동안의 삽질로 몇 가지를 완성했다.

1. 이전 창 위치와 크기를 기억해 복원하고
2. 적당히 커스텀 단축키를 정의하고
3. 입력 모드에서 한글을 사용하다 노멀 모드로 전환되면 영문 모드로 변경되는 기능까지 구성했다.

이 정도까지 완성하였고 이후 서술할 설치 과정까지 삽질을 마치고 보니 2022년부터는 좀 더 본격적으로 사용할 수 있을 듯 하다.

## 이맥스 설치 과정

여러 클라이언트를 설치하고 나름 정리해 본 결과 맥용 커스텀이 가장 잘 되어 있는 아래 패키지를 사용하는 걸로 정했다.

빌드된 패키지 파일은 여기에서도 직접 구할 수 있다.

- https://github.com/railwaycat/homebrew-emacsmacport/releases

brew `cask` 로 설치하지 않으면 추가 디팬던시를 `brew` 가 설치하는데 이게 알아서 관리해준다 해도 소스 수정해서 컴파일하는 작업을 하지 않는 이상 필요가 없다고 본다.

doom emacs 가이드의 첫 번째 추천 클라이언트 자리를 차지할 만 하다.

- https://emacsformacosx.com 의 바이너리 보다 훨씬 빠름
- 빌드된 패키지를 제공함(emacs-plus 는 컴파일해야 해서...)

아무튼 빌드 하지 말자. 여기 https://github.com/railwaycat/homebrew-emacsmacport 를 통해 --cask 로 설치하자.

```shell
$ brew tap railwaycat/emacsmacport
$ brew install --cask emacs-mac-spacemacs-icon
```

> 스페이스맥스의 아이콘은 정말 멋지다.

설치가 완료되면 `/usr/local/bin` 에 `emacs` 실행 스크립트도 추가해 준다. https://emacsformacosx.com/tips 에 있는 내용과 같다.

필요에 따라 `alias em='emacs &'` 으로 사용하자.

### Doom 커스텀

이런 이맥스 편집 환경은 `Spacemacs` 같은 통합 세팅 배포 시스템을 선보이게 되었고 조금 더 마이너한 `Doom Emacs` 구성이 등장하게 되었다.

- [Spacemacs: Emacs advanced Kit focused on Evil](https://www.spacemacs.org/)
- [An Emacs framework for the stubborn martian hacker](https://github.com/hlissner/doom-emacs)

처음에는 스페이스맥스를 사용했었다. 그러다 한동안 흥미를 잃었다. 글쓰기를 다시 시작하고 있는데 입맛에 맞는 타이핑 환경을 찾아 헤매다 둠 이맥스를 다시 보면서 재미를 느껴고 있다.

![Doom Emacs](https://raw.githubusercontent.com/hlissner/doom-emacs/screenshots/main.png)

주 사용처는 취미 코딩 용 `Go` 개발 환경 구성과 블로그 글 쓸 때와 메인 에디터로 선택했다. 어설프지만 ORG 모드 연습도 하고 있는데 업무일지 작성할 때 사용 중이다.

어느 정도 구성을 마치고 본격 사용을 시작했다. 

여유 있을 때 여길 보는게 제일 좋다.

- [DoomCasts: Emacs Doom Screencasts - Zaiste Programming Youtube channel](https://www.youtube.com/playlist?list=PLhXZp00uXBk4np17N39WvB80zgxlZfVwj) 

> 28개의 클립이 있다. 둠 이맥스의 매력을 느껴 볼 수 있다.

{{< youtube rCMh7srOqvw >}}

### 설치 과정

둠 이맥스의 설치 과정은 공식 문서를 따라하는게 가장 좋다. 위에서 추천한 이맥스 패키지 고르는 과정만 주의하자.

`fd`,`ripgrep` 을 필요로 한다. 미리 설치해 두자.

### 재설치 

뭔가 세팅이 꼬였을 경우 쉽게 다시 설치할 수 있다. 

둠 이맥스의 구성은 `~/.doom.d/` 에 저장되어 있다. 이 코드만 잘 백업해 두도록 하자. 

> 나는 dot file 관리를 위해 `chezmoi` 를 사용 중이다.

이맥스 애플리케이션을 다시 설치할 필요는 없다. 이맥스의 로컬 세팅을 제거하고 둠 환경을 다시 설치하면 초기 세팅을 복원할 수 있다.

```shell
$ rm -rf ~/.emacs.d/.local
$ ~/.emacs.d/bin/doom install
```

[^1]: 새해 첫 글에 이맥스를 소개하기 위해 부랴 부랴 작성 중이다.
