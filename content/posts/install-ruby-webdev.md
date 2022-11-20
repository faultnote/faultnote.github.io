---
title: Install Ruby and WebDev Friends
date: 2007-12-16 18:06:00
tags: 
- ruby
- web development
description: Ruby 는 일본인 마츠가 만들어낸 언어입니다. 자세한 설명은 아래의 공식 홈페이지를 참고하세요.
---

> `20071216 루비ruby 설치` 라는 제목으로 시작한 첫 포스트네요. 레일즈와 몽그렐 서버를 구동하는 과정을 공부했나 봅니다. 지금은 너무 먼 얘기네요.

루비온 레일즈ruby on rails 를 사용하기 위해서는 루비ruby 라는 언어를 설치해야합니다.

Ruby 는 일본인 마츠가 만들어낸 언어입니다. 자세한 설명은 아래의 공식 홈페이지를 참고하세요.
[http://www.**ruby**-lang.org/ko/](http://www.ruby-lang.org/ko/)

![ruby_install.jpg](/images/ruby-webdev/ruby_install.jpg)

윈도우용을 기준으로
[http://rubyforge.org/projects/rubyinstaller/](http://rubyforge.org/projects/rubyinstaller/)
에서 원클릭 인스톨러를 다운로드 합니다.

![ruby_win_installer.jpg](/images/ruby-webdev/ruby_win_installer.jpg)
![ruby_win_installer_download.jpg](/images/ruby-webdev/ruby_win_installer_download.jpg)

자주 사용하시는 손에 익은 에디터가 있다면 SCite 라는 항목은 체크 해제 하여 설치하지마세요.

![ruby_win_install-check.jpg](/images/ruby-webdev/ruby_win_install-check.jpg)

(참고로 루비온레일즈를 위한 에디터를 하나 추천합니다. [http://www.plasmacode.com/](http://www.plasmacode.com/) 에서 배포하는 RoRED 입니다.)

반드시! Gem 을 설치하셔야 합니다.
설치가 완료되면 해당 디렉토리에서 결과를 확인.

젬이 설치 되어있다면
레일즈를 온라인으로 설치할 수 있다.

![rails_install_command.jpg](/images/ruby-webdev/rails_install_command.jpg)

커멘드라인에서 (Windows+R > cmd ) 아래처럼 입력한다.

**gem install rails --include-dependencies**

![rails_install.jpg](/images/ruby-webdev/rails_install.jpg)

과정은 인터넷을 통해 다운로드 받아서 설치가 된다. 따라서 인터넷에 접속이 가능해야한다.

![rails_install_ing.jpg](/images/ruby-webdev/rails_install_ing.jpg)

설치후

테스트용 웹서버를 설치하자. 루비에서 기본으로 제공하는 웹브릭Webrick 서버가 있지만 성능이 매우 낮은 상태라고 한다. 선경험자들의 조언을 참고하여 몽그렐mongrel 을 추천합니다.

몽그렐mongrel 설치 역시 젬을 통해 이루어진다.

간단히 `gem install mongrel_service` 을 입력하자.

![mongrel_install.jpg](/images/ruby-webdev/mongrel_install.jpg)

플랫폼에 맞는 몽그렐의 최신버전이 나타난다.
기본적으로 MSWIN32 포맷의 인스톨을 선택하자.

![mongrel_install_select.jpg](/images/ruby-webdev/mongrel_install_select.jpg)

위 그림처럼 각 버전을 선택하고 종속 파일을 설치해주면 몽그렐의 설치가 끝난다.

![mongrel_install_ing.jpg](/images/ruby-webdev/mongrel_install_ing.jpg)

레일즈 프로젝트를 생성한 후 몽그렐을 시작할 수 있다. (이 내용은 다음 포스트에)

> 다음 포스트에... 는 지켜지지 않았습니다. JSP/PHP 하다, 노드를 영접했기 때문이죠... 그 사이 내용은 [예전 블로그](https://faultnote.tistory.com/) 에서 확인할 수 있습니다.
