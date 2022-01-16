+++
title = "Doom Emacs 익숙해지기"
slug = "doom-emacs-guide-1"
date = "2022-01-15T21:38:05+09:00"
author = "soomtong"
cover = "/posts/images/cover-real_programmers.png"
tags = ["editor", "emacs", "org mode"]
keywords = ["doom", "emacs", "shortcut"]
description = "둠 이맥스 익숙해지기 - 글쓰기와 할 일 관리를 한 번에!"
showFullContent = false
readingTime = true
+++

> [xkcd](https://xkcd.com/378/ "Explain xkcd: It's 'cause you're dumb.") 의 [설명](https://www.explainxkcd.com/wiki/index.php/378:_Real_Programmers "The fact that Emacs already has a command for this simply exacerbates the other programmers' frustration with modern coding tools.")을 심각하게 받지 말기 😁 

## 글쓰기와 이맥스

나는 NodeJS 로 백엔드 개발을 하는데 Jetbrains 의 Webstorm 을 사용하고 있다.
예전 Phpstorm 시절부터 고려하면 10년 가까이 젯브레인즈의 제품을 사용하고 있는 것이다.
2010년 경에 사용하던 Eclipse 기반의 단축키 일부를 차용하고 몇 년간 손에 맞게 수정한 버전의 단축키 세트를 사용하다 2019년부터 ideavim 을 통해 Vim Model 편집 기반의 조합으로 일부 수정하여 지금까지 사용 중이다.

코드 작성은 그렇다 하더라도 글을 쓰거나 메모를 남기는 경우에 Webstorm 을 사용하기는 곤란하고 - 특정 시기와 조건에서 한글 입력에 자유롭지 못한 경우가 있었다. 요즘은 큰 문제가 없지만 메모 남기려고 IDE 를 올리는 건 이상하지만, Emacs 라면... 가능할지도 모르겠다. - 주로 서브라임 텍스트를 활용해 메모를 남기거나 할 일 목록을 적어두는 도구로 사용했다.

회사 업무를 위한 할일 관리는 주로 웹 브라우저 기반의 서비스(SaaS)를 사용하고 있는데 긴 글을 편집할 때나 마우스로 조작이 필요한 경우가 많아 즐겁지가 않다.

모달 에디팅에 집착하기 전에는 Markdown 편집이 가능한 도구로 글쓰기를 해왔지만 해피해킹 배열로 키보드를 변경한 후에는 이 마저도 꺼려지게 되었다. 결국 VSCode, Sublime Text 와 그 외 Apple Reminder 나 MS To-do 같은 툴을 통해 글쓰기와 메모/할일 관리 등을 수행하였다.

요즘 프로그래머들은 Editor War 겪지 않지만 예전에는 이런 취향을 두고 주기적으로 :innocent: 논의를 하는게 재미있는 일이었다. 
요즘 웹 개발자들이 이맥스를 경험하는 건 흔치 않지만 나는 운이 좋아 Evil 모드가 있는 [Spacemacs](https://www.spacemacs.org/ "The best editor is neither Emacs nor Vim, it's Emacs and Vim!") 사용했고, 어떻게 기억하고 있는지 모르지만 바닐라 이맥스를 종료하는 방법도 알고 있었다.

2021년부터는 Doom Emacs 를 사용하기 위한 시간을 만들어 보고 있다. 국내에는 이맥스 사용자가 얼마나 계신지 모르겠지만 이 편집기(그 이상이지만)에 대한 글도 많지 않다. 그나마 [seorenn 님의 Emacs 포스트](https://seorenn.github.io/note/doom-emacs.html)가 많이 정리되어 있다.

### 투두, 노트, 저널 그리고 작업 모드(org mode)

내 경우, 글을 쓴다는 것에는 복합적인 요소가 포함되어 있다. 생각의 나열일 수 있고 구조화된 맥락인 경우도 있고, 단순히 오늘 벌어진 일이나 앞으로 하고 싶은 일들의 기록일 수도 있다. 그런 면에서 글쓰기를 일반 텍스트 에디터나 워드프로세서에서만 하는 것은 부족했다. 
특히 워드프로세서로 글을 쓰는 건 엄청난 고통이다. 한때는 '아래아한글' 같은 워드 프로세싱 애플리케이션으로 책을 만들어 본 적도 있지만 다시 책을 쓴다면 저작 도구와 편집 도구를 분리하여 사용할 것이다.

그러고 보니 몇 년 전 학교에서 사용한 책은 VSCode 의 마크다운 모드로 저작을 하고 애플 Pages 로 마무리를 했었다.

> 나의 글쓰기는 책을 편집하는 것이 아니니 텍스트 에디터로도 충분하다. 단, 적당한 에디터가 필요하다.

현 시점에서 이맥스 환경은 이런 요구를 만족시켜주는 최고의 환경이 되고 있다.

#### 작업 모드

이맥스의 '오그모드' 라고 불리는 작업 모드[^1]는 특정 폴더를 대상으로 그 안에 있는 org 파일들에 대해 주어진 조건에 맞는 텍스트를 작업 모드의 대상으로 만들어 준다. 그리고 날짜/시간을 추가하고 트래킹 할 수 있는 기능이 포함되어 있어 일정 관리 도구로도 손색이 없는 수준으로 발전되어 있다.

- [Emacs Org Mode Demo 2021](https://www.youtube.com/watch?v=hnMntOQjs7Q)

{{< youtube hnMntOQjs7Q >}}

이 5분이 안되는 영상을 보면 'Org Mode' 의 매력을 느낄 수 있을 것이다.

[^1]: org mode - 이맥스에서 특정 확장을 위한 구성을 mode 라고 부르고 작업 관리를 위한 가장 유명한 도구로 org mode 가 있다.

#### 투두 

일반적인 `[x]` 같은 표시부터 명시적으로 `TODO` 등의 내용으로 할일 목록을 구성할 수 있다. 아래 소개할 `org-deirectory` 에 있는 투두 목록이 들어간 파일은 별도의 투두 리스트로 추려질 수 있다.

- `SPC n t`: `org-todo-list` 명령으로 할 일이 담긴 버퍼를 연다.

처음 시작한다면 `SPC n n t` 를 통해 기본 todo 파일을 생성하고 시작해 볼수 있다.

```python
** PROJ 프로젝트 이름
*** HOLD OOOO 북마크 대응 API 검수
DEADLINE: <2022-01-21 Fri>
*** STRT XXXX 시청 기록 API 대응
DEADLINE: <2022-01-07 Fri>
*** DONE MMMM 처리 스크립트 보완
DEADLINE: <2022-01-05 Wed>
: 추가 설명...
```

뭐 이런 내용을 작성하면 에디터에서는 이렇게 보인다.

![org mode - todo list](/posts/images/org_mode-todo-lookslike-1.png "나쁘지 않다.")

작업 모드(org mode)에서 기본 구성으로 제공되는 `todo.org` 파일만 관리해도 적당히 쓸만한 할 일 관리를 수행할 수 있다.

`SPC n t` 로 즉시 전체 할 일 목록 버퍼를 띄울 수 있다. 할 일들은 꼭 `todo.org` 파일 안에 있지 않아도 된다. 작업 모드의 지정된 폴더 안에 있는 org 파일은 모두 대상이 되고 각 아이템에서 기록이 있는 파일로 즉시 이동이 가능하다.

투두 항목은 단축키로 상태와 중요도를 변경할 수 있고 위/아래로 위치를 이동시킬 수 있다. 따로 잘라내고 붙이는 작업을 하지 않아도 순서를 관리할 수 있다.

- `M-j/k`: `move subtree up/down` 으로 동작시킬 수 있다. `M` 으로 표기되는 Meta 키는 `Ctrl` 키와 함께 이맥스에서 사용되는 조합키로 맥OS 에서는 `Opt` 키가 그 역할을 한다.

이맥스의 단축키 시스템은, 아직 그 깊은 곳까지 가지 못했지만 방대하고 유연해서 Vim/Neovim 에서 조합할 수 없었던 기능키를 쉽게 적용할 수 있다. 

좋은 단축키 환경을 구성하는 것은 코더로서도 중요하고 문서 생산성 향상에 매우 중요한 역할을 한다고 믿고 주장하는 입장이어서 Evil-mode Emacs 는 네이티브 Vim 환경보다 훌륭하다.

#### 노트

그동안 서브라임 텍스트로 빠르게 메모하던 내용들을 이맥스의 작업 모드 `notes.org` 로 이관해 사용하고 있다.

이맥스 작업 모드의 노트 생성은 캡처 기능과 함께 기본 템플릿을 구성하고 있어 노트를 생성한 날짜를 따로 입력하지 않아도 편리한 부분이 있다. (이맥스에서는 현재 날짜/시각을 입력하는 별도의 툴이 있기도 하다.)

![org mode calendar](/posts/images/org_mode-calendar-timestamp-1.png "타임스템프는 두 종류가 있다.")

예전에 하루패드 사용할 때에도 입력 즉시 현재 날짜와 시각을 생성하는 기능이 매우 유용했는데 이맥스에서 그 기분을 다시 맛보니 글쓰기가 더 재밌어진다.

- `C-c .`: `org mode` 환경 안에서 현재 위치에 날짜를 추가한다.
- `Shift+H/L`: `org mode` 환경의 날짜 텍스트 위에서 이전/다음 일로 변경한다.

나의 노트에는 짧은 메모들을 담고 있다. 직접 파일을 열고 편집하기 보다 다른 상황 다른 맥락에서 급히 메모 남길 때 사용한다.

![org mode my first notes](/posts/images/org_mode-first-note.png "부정확한 정보가 있을 수 있다.")

- `SPC n n n` 또는 `SPC X`: 글로벌 하게 동작하는 키로 현재 컨텍스트를 기반으로 노트를 생성한다.
- `SPC n S`: 작업 모드로 지정된 폴더의 모든 문서를 검토해 노트의 헤더로 이동할 수 있다.

#### 저널

저널은 작업 모드에서 제공하는 기본 템플릿 중 가장 맘에 드는 구성이다. 우선 저널 생성을 위해 아무 내용이나 캡처하여 새 파일을 생성하자.

- `SPC n n j`: 저널 파일이 없으면 새로 만들 수 있고 있으면 현재 컨텍스트를 참조하는 새 항목을 생성한다.

생성되는 헤더에는 현재 일자와 시각 정보가 있다. 나는 이걸 개인 일기로 사용하고 있다. 이 안에서는 할 일도 함께 정의 할 수 있다.

![org mode first journal](/posts/images/org_mode-first-journal.png "가끔은 남 얘기도 쓴다.")

저널을 통해 자유롭게 글을 쓰고 할 일을 만들고 이들을 정리할 수 있다는 것을 알게 된 후부터 본격적으로 이맥스로 글쓰기를 해보려고 시도하고 있다.

## 인박스 구성

### 업무 일지의 예

회사 생활 하면서 매일 매일 일지를 기록하고 있다. 처음에는 마크다운 에디터에서 기록을 시작했고 편집 환경을 Vim 같은 모달 에디팅 환경으로 개선하면서 업무 일지도 Vim 으로 편집했다. 쉘 환경에서 `alias` 를 구성하고 구글 드라이브의 특정 파일을 편집하는 명령으로 즉시 즉시 Vim 에서 일지의 내용을 편집했다.

구글 드라이브로 문서를 동기화하고 있었는데 `md` 파일을 프리뷰하는 과정이 필요해 아예 지난 해는 구글 닥스의 문서를 직접 편집했다. 그러다 모달 에디팅 환경이 아닌게 너무 불편해 크롬 익스텐션을 일부 수정해 모달 에디팅 환경을 구성해 사용하기도 했다. 그래도 키보드 만으로 웹 브라우저에서 글을 쓰는 것은 쉽지 않았다.

올해는 이맥스를 통해 업무 일지를 쓰고 있다. 아이클라우드 동기화를 켜고 인박스에 올해 업무일지 파일(.org)을 만들고 이 문서를 편집한다.

일정 기반의 할 일 등 아젠다를 작성하여 관리하고 있는데 효율이 좋다.

![업무일지2022](/posts/images/org_mode-work-log-journal.png "오그모드 너무 좋아.")

### 저장 위치

하나의 장비(PC)로 글을 쓰는 환경이 아니라면 기기 간 동기화가 가능한 솔루션을 통해 진행하는 것이 좋다.

애플의 iCloud 드라이브나 아이클라우드를 통해 지원하는 데스크탑/도큐멘트 폴더 동기화 기능이나 구글 드라이브, 드랍박스 등의 동기화가 가능한 대상을 인박스로 구성하는 것을 추천한다.

내 경우, 데스크탑에 Inbox 폴더를 두었다.

```shell
# ~/.doom.d/config.el 
;; If you use `org` and don't want your org files in the default location below,
;; change `org-directory`. It must be set before org loads!
(setq org-directory "~/Desktop/Inbox")
```
자주 보는 곳에 두고 자주 사용할 수 있도록 했다.

### 자동 저장

Doom Emacs 에 아쉬운 점이 하나 있는데 자동 저장 기능이 디폴트가 아니라는 점이다.
Webstorm 이나 VSCode 를 사용할 때는 항상 자동 저장을 켜고 사용했고 다른 사람에게 안내할 때는 반드시 이 옵션부터 활성화 하라고 지시하기도 했다.

물론 적당한 확장 코드를 통해서 이 기능을 추가할 수 있는데 그냥 써보기로 했다. Vim 과 다르게 `CMD+S` 를 통해 저장이 가능하니 아주 힘든 것은 아니다.
수정된 버퍼가 남아 있는 동안 이맥스를 종료할 경우에 한 번 씩 더 물어보니 습관을 바꿔보는 것도 괜찮겠다.

## 둠 이맥스의 미래

[Henrik Lissner](https://github.com/hlissner) 가 시작한 이 프로젝트는 조만간 GitHub 의 doomemacs organization 으로 이관될 듯 하다. 그 밑 작업들이 계속 진행되고 있으며 Spacemacs 만큼 중요한 프로젝트가 될 것 같다. 

![DoomEmacs Organization in GitHub](/posts/images/doom-emacs-org-account.png)

둠 이맥스가 계획하고 있는 로드맵을 보면 이 에디터 환경에 배팅해도 좋을 듯 하다.
