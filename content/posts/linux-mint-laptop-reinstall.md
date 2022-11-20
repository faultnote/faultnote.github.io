---
title: 개발용 리눅스 랩탑 세팅 기록
tags:
- linux mint
- development environments
description: 작업용 랩탑을 하나 장만했는데 인텔 7 세대 I5 CPU 가 장착된 녀석이고 인텔의 최근 와이파이 칩셋을 탑재한 모델이라 4.4 커널 버전의 배포본에는 해당 장치를 지원하지 못하는 문제가 있어 며칠 전에 나온 17.04 우분투를 설치하게 되었다.
readingTime: true
date: 2017-04-21 23:58:01
author: soomtong
---

작업용 랩탑을 하나 장만했는데 인텔 7 세대 I5 CPU 가 장착된 녀석이고 인텔의 최근 와이파이 칩셋을 탑재한 모델이라 4.4 커널 버전의 배포본에는 해당 장치를 지원하지 못하는 문제가 있어 며칠 전에 나온 17.04 우분투를 설치하게 되었다.

(업데이트 1) 사실 4.4 커널로 설치하고 커널 업데이트를 하면 되는데 이때는 그런 생각을 못했다. 그래서 나중에 민트로 다시 설치하게 되었다.
(업데이트 2) 2017년 6월 민트 18.2 베타가 시작되었는데 여기에 탑재된 커널 버전이 최신 인텔 무선 칩셋을 인식하는 4.8 버전이었다. 여전히 패키지 베이스는 우분투 16.04 로 적당히 사용할 수 있다.

## 한성 TFG13

![인민에어의 시대는 끝났다](/images/linux-mint-laptop/hansung-tfs13.jpg)
> 인민에어의 시대는 끝났다!

사실 이 랩탑을 주문하기 전 13~14인치 대의 다양한 노트북을 살펴보았는데 처음 생각과 다르게 점점 예산이 높아지는 현상을 발견하고 처음 마음 먹은대로 인민에어를 사기로 마음을 굳혔다.

하지만 장비 중 가장 중요하게 생각하는 램 스펙에 있어 인민에어의 램 뱅크는 싱글 채널이었고 포트가 조금 부실했고 영문 각인을 고를 수 없다는 몇가지 불만이 있었다. (세벌식 사용자는 두벌식 한글 각인이 필요 없다. 그 중에도 신세벌 자판을 사용하고 있다.) 그러던 중 노트북MD 역할 비슷하게 하던 댓글 관리자의 추천을 보고 이 랩탑을 알게되었다. (물론 수시로 들락거렸던 한성 몬스터랩 웹사이트에도 이 노트북이 있던건 알고 있었지만 자세히 볼 생각을 못했었다.)

대부분 저가 랩탑의 생산지는 중국이다. 이 랩탑을 제조한 회사는 나름의 지명도가 있는 듯한 느낌을 댓글창에서 발견했다. (뭐 그런가보다 했다.) 크기나 무게는 인민에어와 비교해 꿀리지 않았고 신세대 포트를 탑재하고 있었으며 나름 적당한 키보드 레이아웃을 가지고 있던게 맘에 들었다.

갈색 계열의 상판으로 구성된 모델도 출시하고 있어 이 갈색 상판 모델을 골랐다. 램 슬롯은 듀얼채널로 확장성에 있어서도 나쁘지 않은 스펙이었다. 홈페이지에는 8기가 단일 모델로 소개하고 있지만 오픈마켓 주문하는 곳에서는 램 확장 옵션이 있었다.

램만 16 기가로 확장하니 80 여 만원이 결제되었다. 배송은 주말 포함해 4~5일 걸린 듯 하다. (금요일에 주문해 다음 주 화요일에 도착했다.)

### 장/단점

키 배열이 무척 맘에 든다. 대부분의 랩탑에서 제공하는 컴팩트한 키배열에 추가로 HOME, END, PGUP, PGDN 키를 제공하고 있다. 화살표 키 역시 기존 맥북이나 신형 맥북 스타일이 아니고 별도의 위치에 자리잡고 있는 점도 맘에 들었다. 컨트롤 키가 가장 좌측에 위치한 점도 매우 만족스럽다. 다만 우측 컨트롤 키로 사용될 '한자' 변환 키는 비정상적으로 컸지만 나름 슬기롭게 해결했다.

액정도 가격대비 생각하면 볼만한 편이고 넓직한 베젤도 그럭저럭 괜찮다. 커다랗게 'HANSUNG' 이라고 적힌 폰트도 깔끔하다.
다만 상판이 살짝 부실한 편인데 무게를 위해 사용한 플라스틱 상판이 잘 눌린다. 그리고 USIM 슬롯이 있는데 막아두던가 다른 뭔가 기능을 하도록 했으면 어땠을까 생각해 봤지만 이 정도 가격에 이 정도 마감 품질이면 매우 만족!

리눅스 데스크탑 환경을 위해 구입한 모델이라 당연히 OS 는 설치되어 있지 않은 모델을 골랐다. (윈도우즈 개발 환경을 떠난지 4년이 넘어간다. ~~요즘 힙스터는 윈도우즈 10 의 리눅스 서브 시스템에서 개발한다고 하더라...~~)

최신 인텔 칩셋을 탑재한 관계로 리눅스 커널 4.8 이상에서 내장된 WIFI 칩셋을 인식한다. 기존 데스크탑에 사용하고 있던 리눅스 민트는 현재 16.04 베이스의 4.4 커널을 기반으로 하고 있어 준비해둔 리눅스 민트 최신 버전으로는 와이파이를 잡을 수 없었다. 결국 우분투 최신 버전을 설치하기로 했다.

## 우분투 메이트

17.04 우분투 디폴트 버전을 설치했는데 유니티 UX 는 도통 적응이 불가능했다.

결국 그 옛날 우분투의 기본 데스크탑 환경이었던 Mate 버전을 설치했다. 매우 만족! 다양한 커스텀이 가능하고 특히 키보드 단축키 지정하는 부분이 유니티 버전에 비해 풍부하며 우분투 기본 버전과 유사한 부분은 특정 사용자에게 시나몬 환경보다 나는 느낌도 있다. 리눅스 민트가 아쉬울 때 우분투 메이트도 정말 좋은 선택지라고 느꼈다. 물론 리눅스 민트 메이트 버전도 괜찮을 것 같다는 생각을 했다.

![데스크탑 맘에 든다.](/images/linux-mint-laptop/ubuntu-mate-green.png)

![데스크탑 맘에 든다.](/images/linux-mint-laptop/ubuntu-mate-desktop.png)

### 키보드 단축키 변경

TMUX 에 VIM 만 사용하는 하드코어 콘솔 매니아가 아니라면 GUI 인터페이스에 단축키 사용은 생산성 향상에 큰 영향을 준다. 나름 윈도우를 관리하는데 있어 적절하다고 생각하는 단축키 조합을 정리했다. (페이지 말미에 단축키 패널 캡쳐한 이미지가 있습니다. 그리고 MOD4 키는 윈도우 키 또는 SUPER KEY 로 불리는 키를 의미합니다.)

#### 주요 키 리스트

| BIND ACTION                  | KEY                   | COMMANT     |
| -----------------------------| --------------------- | ------------|
| ADVENCED MATE MENU           | RIGHT CTRL [^1]       | 메인 메뉴를 대체   |
| LOCK SCREEN                  | SUPER+L               |             |
| HOME FOLDER                  | SUPER+E               |             |
| SEARCH                       | SUPER+F               |             |
| RUN APPLICATION              | SUPER+R               |             |
| MAIN MENU                    | DISABLED              | 어드밴스드 메뉴를 사용|
| WINDOW SCREENSHOT            | ALT+PRINT             |             |
| **SWITCH WINDOWS OF APP**    | ALT+`                 |             |
| HIDE ALL (DESKTOP)           | SUPER+D               |             |
| **WORKSPACE 1~4**            | SUPER+F1~F4           |             |
| SWITCH WORKSPACE LEFT        | CTRL+SUPER+LEFT       |             |
| SWTICH WORKSPACE RIGHT       | CTRL+SUPER+RIGHT      |             |
| **SWITCH PREVIOUS WORKSPACE**| SUPER+TAB             |             |
| WINDOW MENU                  | SUPER+SPACE [^2]      |             |
| TOGGLE FULL SCREEN WINDOW    | SUPER+DOWN            |             |
| TOGGLE MAXIMIZE WINDOW       | SUPER+UP              |             |
| CLOSE WINDOW                 | SUPER+Q [^3]          |             |
| MINIMIZE WINDOW              | SUPER+N               |             |
| MOVE WINDOW                  | SUPER+M               |             |
| RESIZE WINDOW                | SUPER+S               |             |
| MAXIMIZE VERTICALLY          | SUPER+END             |             |
| MAXIMIZE HORIZONTALLY        | SUPER+HOME            |             |
| TILE RIGHT                   | SUPER+RIGHT ARROW     |             |
| TILE LEFT                    | SUPER+LEFT ARROW      |             |
| MOVE RIGHT SIDE              | SUPER+ALT+RIGHT ARROW |             |
| MOVE LEFT SIDE               | SUPER+ALT+LEFT ARROW  |             |
| MOVE TO LEFT WORKSPACE       | SHIFT+SUPER+LEFT      |             |
| MOVE TO RIGHT WORKSPACE      | SHIFT+SUPER+RIGHT     | .           |

[^1]: 한성 TFG13 의 오른쪽 컨트롤 키가 무식하게 큰 관계로 - 게다가 거기엔 '한자' 변환 단축키라고 각인이 되어 있다. - 이 상징적으로 큰 키를 어떤 용도로 사용할까 고민핸는데 메이트의 `ADVENCED MATE MENU` 가 매우 적당한 대상이 되었다.
[^2]: 보통 ALT+SPACE 를 사용하는데 나는 SYNAPSE - 리눅스용 스팟라이트(알프레드 같은) - 를 이 키에 사용하는 관계로 변경해 사용한다. 맥에서도 이 배열을 사용하고 있다.
[^3]: 이 키 조합이 가능해 정말 기분이 좋았다. 맥 환경에서 사용하는 앱 종료 단축키를 그대로 쓸 수 있었기 때문이다. ALT+F4 는 손목이 꺾이기도 하고 두 손을 사용해야 하기도 하고 내겐 너무 멀다.

### ADVENCED MENU

우분투 메이트에서는 윈도우즈 메뉴 스타일의 기본 메뉴를 제공함과 더불어 조금 더 많은 영역을 사용하는 어드밴스드 메뉴를 제공한다. 이 한성 랩탑의 문제인지 아니면 우분투 메이트 버전의 문제인지 윈도우 키(수퍼 키)를 눌러도 메뉴가 팝업하지 않았다. 그래서 SUPER+TAB 키로 할당해 사용하다가 비정상적으로 크게 자리를 차지하는 한자키를 사용하면 어떨까해서 이런 저런 테스트를 하게 되었다.

어드밴스드 메뉴는 별도의 단축키를 바인딩할 수 있는 기능을 제공하는데 기본 메뉴와 다르게 특수키가 단독으로 눌려도 반응을 하는 것이었다. 마침 더 나은 디자인의 메인 메뉴를 사용하면서도 적당한 키 조합을 유지할 수 있게 된 것을 발견해 세팅하는 내내 무척 즐거웠다.

![어드밴스드 메뉴](/images/linux-mint-laptop/main-menu-key.png)

## 민트 시나몬

설치할 때 와이파이를 잡지 못하더라도 그냥 설치하고 나중에 커널 업데이트를 통해 와이파이 드라이버를 활성화 시킬 수 있다른 것을 생각해 낸 후 리눅스 민트를 다시 설치할 계획을 세웠다. 역시 민트 시나몬 환경이 내 취향인 것 같다.

![데스크탑 맘에 든다.](/images/linux-mint-laptop/mint-beta.png)

### 백업과 복원

홈 폴더만 잘 백업해 두고 그대로 복원하면 왠만한 환경들은 복구된다. 그동안 사용하던 앱 목록이나 서비스 정도만 기록해 두면 될 것 같다.

#### 설치된 패키지 리스트

가급적 안정된 복원 작업을 위해 전체 파일 리스트를 한 부 가지고 있는 것도 나쁘지 않다.

```
$ tree -ahv -o ~/tree_list.txt /
```

그리고 전체 패키지 목록도 확인해 보는 것이 좋다.

```
$ apt list --installed > ~/package_list.txt
```

### 단축키 바인딩

기존에 세팅했던 것과 비슷하다 또한 데스크탑에서 사용하고 있는 세팅을 그대로 적용할 수 있었다.

## 애플리케이션 설치

대부분 패키지들을 기본 레포지토리와 PPA 를 통해 설치했다. 쉘 환경 몇 건과 폰트들 설치한 것을 제외하면 모두 스크립트로 만들 수 있을 것 같았다. 직접 다운로드해 설치하지 않고 콘솔 명령을 통해 설치할 수 있는 패키지 레포지토리를 제공하는 (개인적으로) 중요한 애플리케이션은 대충 아래와 같다.

- 메인 에디터: 마이크로소프트 비주얼 스투디오 코드
- 서브 에디터: 서브라임텍스트
- 메인 브라우저: 구글 크롬 브라우저

### 추가 레포지토리 목록

- aacebedo-fasd
- typora
- wine-hq
- dart{stable:unstable}
- go-for-it-team
- google-chrome
- jonathonf-rustlang
- kritalime-ppa
- neovim-ppa
- nodesource
- oibaf-graphics-driver
- remmina-ppa-team
- skype-stable
- slack
- sublime-text
- thomas-schiex-blender
- ubuntuhandbook1-corebird
- ubuntu-toolchain-r-test
- virtualbox
- vscode

### 대충 정리한 콘솔 명령 히스토리

운영체제 설치를 마치고 작업한 과정을 대충 남겨보았다. 다음에도 이런 식으로 개발 장비를 세팅하기 위해 히스토리를 검토해 정리해 둔다.

```
sudo apt update
sudo apt install zsh
chsh -s /usr/bin/zsh
sudo reboot now

sudo apt install build-essential
sudo apt install ranger
sudo apt install vifm
sudo apt install curl
sudo apt install git
sudo apt install tmux
sudo apt install vim

select-editor
sh -c "$(curl -fsSL https://raw.githubusercontent.com/liuchengxu/space-vim/master/install.sh)"
ln -s ~/.space-vim/init.vim ~/.vimrc
cd .space-vim/private/
vi config.vim
vi packages.vim

git clone https://github.com/tarjoilija/zgen.git "${HOME}/.zgen"
git clone https://github.com/unixorn/zsh-quickstart-kit.git ~/.zsh-quickstart-kit
ln -s ~/.zsh-quickstart-kit/zsh/.zgen-setup .
ln -s ~/.zsh-quickstart-kit/zsh/.zsh_aliases .
ln -s ~/.zsh-quickstart-kit/zsh/.zsh_functions .
ln -s ~/.zsh-quickstart-kit/zsh/.zshrc .
cd .zshrc.d/
vi alias
vi path

sudo apt install gitg
sudo apt install mono-devel
sudo apt install openjdk-8-jdk-headless
sudo apt install ocaml opam

curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
apt update
sudo apt install -y nodejs
vi .npmrc

sudo add-apt-repository ppa:jonathonf/rustlang
sudo apt install -y rustup

wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
sudo sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list'
apt show google-chrome-stable
sudo apt install google-chrome-stable

curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main" > /etc/apt/sources.list.d/vscode.list'
sudo apt update
sudo apt install code

wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
sudo apt install sublime-text

curl https://repo.skype.com/data/SKYPE-GPG-KEY | sudo apt-key add -curl https://repo.sky
echo "deb https://repo.skype.com/deb stable main" | sudo tee /etc/apt/sources.list.d/skypeforlinux.list
sudo apt update
sudo apt install skypeforlinux

sudo apt install docker.io

sudo add-apt-repository ppa:remmina-ppa-team/remmina-next
sudo apt update
sudo apt install freerdp2-x11

sudo apt install gimp kazam

ssh-keygen

sudo reboot now
```

## 한글 입력 환경

님프 입력기와 다양한 세벌 자판이 들어간 `3beol` 님의 한글 라이브러리를 사용하고 있다. 한/영 변환을 위해서는 SHIFT+SPACE 키와 오른쪽 ALT 키를 사용한다.

> 꾸준히 한글 입력 환경 레포지토리와 라이브러리를 제공/갱신해주시는 분들께 감사드립니다.

- https://launchpad.net/~createsc/+archive/ubuntu/3beol
- https://github.com/choehwanjin/libhangul
- https://github.com/cogniti/nimf

---

![보관용 단축키 리스트](/images/linux-mint-laptop/shortcut-panel.png)
