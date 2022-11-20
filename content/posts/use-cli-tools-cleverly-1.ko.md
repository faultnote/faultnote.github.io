+++
title = "생산성을 높이는 git 사용 테크닉"
slug = "use-cli-tools-cleverly-1"
cover = "https://user-images.githubusercontent.com/8456633/174470852-339b5011-5800-4bb9-a628-ff230aa8cd4e.png"
date = "2020-11-16"
author = "soomtong"
tags = ["development environments", "terminal"]
keywords = ["lazygit", "gitui"]
description = "CLI 툴을 통해 생산성을 높이는 방법"
showFullContent = false
readingTime = true
+++

## git 버전 관리 시스템

코딩 기술도 중요한데 요즘은 협업을 위한 버전 관리 시스템을 잘 사용하는 것이 더 중요해진 느낌이다.

### gitconfig

### alias

## 멀티 브랜치와 체리픽 그리고 PR 전략

### 개인 브랜치 중심 작업 시나리오

### 피처 브랜치 중심 작업 시나리오

## CLI 툴

### 여러 브랜치를 볼 때

lazygit 이 유용하다.

- 특정 브랜치의 커밋을 보고 브랜치를 삭제하는 경우
- VI like 키 바인드 기본 지원

### 단일 브랜치 기준 작업시

gitui 가 유용하다.

- 대부분의 GUI 클라이언트 처럼 인터페이스를 제공
- VI 키 바인딩 가능
- 더 빠른 속도 보장

사용자 환경에 따라 push 동작이 완료되지 않는 버그가 있다.

- https://github.com/extrawurst/gitui/issues/495#issuecomment-854948221

를 참고해 수정했다.
