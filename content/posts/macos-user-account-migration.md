---
title: 이상이 있는 macOS 사용자 계정의 환경을 다른 계정으로 이전하기
date: 2019-01-04 23:07:15
tags: 
- macos
- development environments
description: 맥OS를 새로 설치하지 않고 새 계정을 사용함으로 문제를 해결하고 이전 계정의 세팅을 복원한 과정을 기록
readingTime: true
author: soomtong
---

최근 보안 업데이트 + 사파리 업데이트 (12.02) 후인지 아니면 그 전부터인지 모르겠지만 (왜? 사파리가 메인 브라우저가 아니어서) 사파리의 설정 패널이 뜨지 않는다. 뜨지 않는 것 뿐 아니라 프레퍼런스 메뉴에 들어가면 바람개비 돌고 사파리가 멈춘다.

몇 시간 서핑해도 답이 없다. 단 새 계정으로 들어가 사파리를 실행해 보면 정상적으로 작동한다. 즉, 애플리케이션 자체의 문제가 아니라 해당 계정의 어떤 부분과 충돌한다는 것이다.

그래서 새로운 계정으로 이전할 계획을 세웠다.

macOS 하이시에라 10.13.6 에서 진행하였다.

## 시스템 설정

### 마우스 세팅

![mosue control](system-preferences-mouse-control.png)

-   Mouse > Scroll direction: Natual 을 Off

마우스 관련해 추가 작업할 부분이 있는데 커서 흔들면 포인터 커지는거 불편한 경우가 있는데 어디에서 찾아야 할 지 기억나지 않는 경우가 있다.

![shake mouse pointer](system-preferences-accessibility-display.png)

-   Accessibility > Shake mouse pointer to locate 를 Off

### 키보드 세팅

키보드 세팅은 할게 좀 많겠다.

![keyboard settings](system-preferences-keyboard.png)

조합 키 배열은 다음과 같다. 키보드와 마우스 그리고 카라바이너 세팅까지 모두 같게 적용하면 된다. 예전에는 커맨드키와 옵션키를 여기에서 변경했는데 지금은 카라바이너를 통해 변경해 사용한다. 따라서 이 부분은 그대로 둔다.

-   조합키는 그대로 유지

![additional key swapping](system-preferences-key-swap.png)

텍스트 부분은 크게 상관할 것 없지만 스펠링 부분만 한글 위주로 세팅하면 될 것 같다.

-   앱: 애플리케이션, 이거 은근 편하다.

![text replacement](system-preferences-text-replacements.png)

딕테이션 부분도 한글 위주로 세팅하견 될 것 같다. 데스크탑 키보드에서는 오른쪽 커맨드 키 두 번 누르는 것으로 세팅했다.

![dictation](system-preferences-dictation.png)

-   딕테이션 활성화: Command 두 번

그 외 입력기 부분은 구름입력기 커스텀 버전인데... `모하비`에서 잘 작동할지 걱정이다.

팥알님의 신세벌 바탕에 몇 가지 변경해 사용하고 있다.

#### 단축키

이제 중요한 단축키 부분이다.

Dock 은 기본적으로 감추어진 상태로 사용한다.

> 최근 맥에서 각 윈도우의 가장자리를 더블 클릭하면 해당 방향으로 창 사이즈를 최대화하는 기능을 탑재하고 있다. 마우스로 드래그하면 하단 영역을 최대로 확장할 수 없을 때 더블클릭해서 애플리케이션 윈도우를 확대할 수있다.

![shortcuts 1](system-preferences-shortcuts-1.png)

미션 콘트롤 부분도 조금 다르다. 언제 부턴가 (아마 리눅스 데스크탑 쓸 때 부터?) 이 조합을 사용한다. 은근 편하다.

-   미션 콘트롤: 옵션 + 탭

![shortcuts 2](system-preferences-shortcuts-2.png)

윈도우 관련 부분은 사이클 정도만 사용한다.

-   윈도우 사이클: 커맨드 + 백틱

![shortcuts 3](system-preferences-shortcuts-3.png)

다음 입력 소스로 변경만 활성화해서 사용한다. 최종적으로 이 키를 카라바이너에서 바인딩해 한글/영문을 변경한다.

![shortcuts 4](system-preferences-shortcuts-4.png)

이미지 캡쳐 하는 부분은 메모리에 저장하는 키만 활성화 한다.

추가로 이제는 업데이트가 보장되지 않는 `Skitch`를 사용하는데 스키치는 비슷한 조합으로 `5` 번 키에 할당되어 있다.

![skitch shortcut](screen-capture-tool.png)

서비스에서 제공하는 단축키는 하나도 사용하지 않는다.

![unbind services shortcut](system-preferences-service-shortcuts.png)

스폿라이트는 기본으로 사용한다. 이 조합을 윈도우즈나 리눅스 데스크탑에서도 적용하고 있다.

![shortcuts 5](system-preferences-shortcuts-5.png)

억세서빌리티 부분도 역시 None 으로 세팅한다.

![shortcuts 6](system-preferences-shortcuts-6.png)

추가로 위 3개의 앱에서 사용하고 있는 별도의 단축키가 있다.

-   Typora: Copy as HTML Code 쉬프트 + 커맨드 + D
-   PDF Expert: Show in Finder 쉬프트 + 커맨드 + R
-   Tweetbot: Copy Tweet 쉬프트 + 커맨드 + C

![shortcuts 7](system-preferences-shortcuts-7.png)

> 운영체제 자체에서 개별 애플리케이션의 단축키를 지정할 수 있는 부분은 맥OS 의 최대 강점이라고 할 수 있다. 매우 유연하다.

### 그 외 세팅

> 특이하게 (모하비에서도 그런지 모르겠지만) 상단 메뉴를 다크모드로 세팅하면 워크스페이스 세로 길이를 1 픽셀 더 길게 사용할 수 있다.

큰 의미 없겠지만 이곳 저곳 기록을 남겨 본다.

![general settings](system-preferences-general.png)

데스크탑 배경은 30분마다 랜덤한 이미지를 깔고 있다.

![desktop background](system-preferences-desktop.png)

스크린 세이버는 20분 컷

![screen saver](system-preferences-screen-saver.png)

독 세팅은 별거 없다. 지니 사이즈는 가장 큰 걸로 (어짜피 안보이게 쓰니까) 나머지는 어떻게 사용하든 별 관계 없다.

![dock settings](system-preferences-dock.png)

미션 콘트롤 부분도 별거 없다. 핫 코너는 사용하고 있지 않다.

![mission control](system-preferences-hot-corner.png)

영문 기본 세팅을 하고 있고 뭐 나머지는 특별한 것 없다. 대신 `아이튠즈` 의 경우 주의할 것 이 있다.

![lang and region](system-preferences-lang-region.png)

아이튠즈의 경우 한글 세팅이 아닌 경우 뭔가 오작동하는 것이 있던 것 같다. 아마 정렬 같은 것이겠지? 지금은 기억이 나지 않지만 뭔가 있다.

```shell
 $ defaults read com.apple.iTunes AppleLanguages
(
    ko
)
```

아마 저렇게 때려보면 뭔가 로케일이 표시될 텐데 `ko` 가 필요하다.

FileVault 나 파이어월은 사용하고 있지 않다.

시큐리티 부분에서 나중에 애플리케이션 세팅할 때 필요한 것은 이 정도면 될 것 같다. 억세서빌리티 부분의 항목들이다.

![accessibility permits](system-preferences-security-privacy.png)

스폿라이트의 인덱스 관련한 부분도 손 봐주면 좋다.

특히 Exclude 할 부분은 반드시 세팅해야 한다.

![spotlight index targets](system-preferences-spotlight-indexes.png)

![spotlight privacy to index](system-preferences-spotlight-privacy.png)

`Flux` 애플리케이션 을 사용하고 있어서 Night Shift 는 사용하지 않고 있다. 사실 잘 되는지도 모르겠다. 나중에 고급 모니터가 달린 맥북에서 써보고 맘에 들면 Flux 를 대체하겠지만 나이트 쉬프트 나왔을 때 잠깐 들어본 소개로는 토글 식으로 작동하는 것 같아서 무시할 상황이었다.

![monitor](system-preferences-monitors.png)

![monitor color night shift](system-preferences-monitor-night-shift.png)

에너지 관련 부분은 기본 세팅과 같다.

![energy saver](system-preferences-energy-saver.png)

`iCloud` 부분은 포토만 빼고 다 동기화 하고 있다.

![icloud sync](system-preferences-icloud.png)

스마트 스크롤 설정은 `Grab Scroll` 부분만 사용한다. 완소앱.

![smart scroll app settings](system-preferences-smart-scroll.png)

## 홈브류 세팅

`brew` 는 그냥 `Cakebrew` 프론트엔드에서 쓰고 cache 정리할 때만 터미널에서 작업하고 있다. GUI 를 보는 것이 좀 더 안정적인 느낌이다. 물론 터미널의 데이터도 충분히 인지할 만 하지만 한눈에 보기에는 여전히 GUI 가 좋다.

여기에 없는 바이너리들은 직접 패키지 설치해서 사용하는 편이다. 예를 들면, `fish` 는 그냥 맥용 설치 패키지를 통해 사용한다. 바이너리는 `/usr/local/` 에 설치된다.

그리고 `zsh` 용 `antibody` 도 직접 설치한다.

![homebrew app list](homebrew-list.png)

취미로 엘릭서를 공부하겠다고 마음먹고 얼마 지나지 않아 `nim` 을 알았는데 엘릭서 지울 듯. 잡부에게 얼랭이라니 과분하다.

### npm global

노드를 메인으로 사용하는 관계로 npm 을 통해 사용하는 글로벌 모듈이 좀 있다.

```
/usr/local/lib
├── @zeit/ncc@0.5.5
├── azure-functions-core-tools@2.3.199
├── coffeescript@2.3.2
├── eslint@5.11.0
├── firebase-tools@6.2.2
├── hexo-cli@1.1.0
├── import-js@3.2.0
├── less@3.9.0
├── node-gyp@3.8.0
├── nodemon@1.18.9
├── npm@6.5.0
├── npm-check-updates@2.15.0
├── pm2@3.2.4
├── rebase-editor@2.0.5
├── reveal-md@2.4.0
├── snyk@1.118.2
├── typescript@3.2.2
└── yarn@1.12.3
```

## 애플리케이션 세팅

대부분의 세팅은 드랍박스의 `config` 폴더 안에 보관하고 있다. 드랍박스 동기화 하고 심볼릭 링크 다시 걸면 대부분 해결될 것 같다.

`.config` 나 `.local` 에는 별도 설치한 폰트들도 있으니 잊지 말고 복사하자.

사용중인 애플리케이션은 현재 이 정도인데 구입해두고 안 쓰는 것들도 많다.

![applications 1](applications-1.png)

![applications 2](applications-2.png)

![applications 3](applications-3.png)

그 외 주의할 점이 있다면 `~/Library` 안의 내용을 참고해서 앱 세팅할 때 도움 받을 수 있다. 아니면 깔끔하게 다시 시작할 수 도 있고...

특히, `Application Support`, `Preferences`, `LaunchAgents`, `Fonts` 의 내용들을 버리지 말고 잘 확인하자.

### 계정 이름 관련 설정 정보 변경

`Application Support` 나 `Preferences` 에는 다양한 설정 파일들을 보관하고 있다. 이 중 계정 이름이 들어간 부분을 모두 새로운 계정 이름으로 변경해 주면 좋다.

```
Application Support $ rg Users/old-account
```

반드시 변경될 파일을 확인하고 진행하자.

```
Application Support $ rg Users/old-account | xargs sed -i 's/Users\/sven/Users\/ck/g'
```

이 정도도 괜찮을 것 같지만 잘 안되더라.

최종적으로 `rg` 로 변경 대상 파일 명단을 뽑아서 다음 명령으로 파일 내용을 변경했다.

```
$ rg -l Users/oldaccount
$ perl -i -pe 's/Users\/oldaccount/Users\/newaccount/g' filePath...
```

### 윈도우 매니저 세팅

여러가지 윈도우 배열 매니저들이 있는데 윈도우타이디를 오랜동안 사용해왔다. 그냥 익숙해서 다른 것으로 바꾸기도 귀찮다.

![window tidy](window-tidy-1.png)

![window tidy options](window-tidy-2.png)

![window-tidy position](window-tidy-3.png)

![image-20190102040216874](window-tidy-4.png)

윈도우타이디와 함께 쓰는 윈도우 퀵 어레인지 앱인데 이것도 오랜동안 사용해서 그냥 계속 쓴다. 둘다 무료이다.

![spectacle key bindings](spectacle.png)

### 카라바이너 세팅

구름입력기와 함께 한글 전환 키 바인딩에 탭을 사용하고 있는데 카라바이너가 그 역할을 해 준다.

![karabiner](karabiner-1.png)

프로파일만 잘 복사해 사용하면 될 것 같다.

최근 카라바이너의 Complex Modifications 를 활용해 한/영 전환을 좀 더 깔끔하게 수행하는 세팅을 추가했다.
이 방법을 사용하면 위 캡쳐 이미지의 caps_lock 부분을 제거해도 된다. 캡스락 기능을 별도의 키에 세팅해 주지 않아도 되는 점도 좋고 네이티브 한글 입력기 처럼 캡스락을 오래 눌러 영문자 케이스를 변경하는 것도 가능하다.

```
{
    "title": "한/영 전환 with Caps Lock",
    "rules": [
        {
            "description": "Caps_Lock to inputMethod toggle with short press but toggle capsLock with long press",
            "manipulators": [
                {
                    "type": "basic",
                    "parameters": {
                        "basic.to_if_alone_timeout_milliseconds": 200,
                        "basic.to_if_held_down_threshold_milliseconds": 200
                    },
                    "from": {
                        "key_code": "caps_lock"
                    },
                    "to": [
                        { "key_code": "vk_none" }
                    ],
                    "to_if_alone": [
                        { "key_code": "f16" }
                    ],
                    "to_if_held_down" : [
                        { "key_code": "caps_lock" }
                    ]
                }
            ]
        }
]
}
```

### 임시 복사 유틸리티

카피&패이스트 유틸리티로 `Clippy` 를 사용하고 있다.

![clippy](clippy.png)

메인 단축키 하나만 쓴다.

### 그 외

홈 메뉴바 상태

![menubar](menubar-1.png)

그리고 애플리케이션 독, 타이포라와 스키치는 고정 애플리케이션은 아니다.

![dock](docks.png)

런치패드 레이아웃은 이렇게 쓰고 있다. 페이지 3 개를 중심으로 주로 쓰는 것 2 페이지 나머지는 폴더에 넣었다.

![launchpad-1](launchpad-1.png)

![launchpad-2](launchpad-2.png)

![launchpad-3](launchpad-3.png)

![launchpad-4](launchpad-4.png)

![launchpad-5](launchpad-5.png)

![launchpad-6](launchpad-6.png)

![launchpad-7](launchpad-7.png)

![launchpad-8](launchpad-8.png)

![launchpad-9](launchpad-9.png)

## 도큐멘트 및 코드 저장소

그냥 복사하고 소유권 만 변경해주면 될 것 같다. 물론 sudo 필요하다. 그리고 상위 폴더의 소유자도 확인해 주도록 한다. 이거 확인 안하고 세션이 저장되지 않는 문제에 엉뚱한 곳에서 삽질했다.

```shell
new_folder $ cp -a /Users/old-account/Library/old_folder .
new_folder $ chown -R newuser new_folder
```

### 정리

운영체제를 새로 설치하는 것 보다 소소한 노력과 적은 위험으로 시스템을 깨끗하게 유지할 수 있는 방법 같다.
