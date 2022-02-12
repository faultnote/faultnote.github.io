+++
title = "로컬 웹 서비스에 Easy-RSA 인증서 적용하기"
slug = "easyrsa-nginx-guide"
date = "2022-02-12T14:05:53+09:00"
author = "soomtong"
cover = "/posts/images/cover-local-cert-easy-rsa.png"
tags = ["certificate"]
keywords = ["nginx", "easyrsa", "certificate"]
description = "openssl 과 easyrsa 를 통해 가장 쉽게 로컬 인증서 관리하기"
showFullContent = false
readingTime = true
+++

> nginx 서버에 로컬 호스트의 도메인을 위한 인증서를 적용하는 가장 쉽고 빠른 방법

## 개인 키/공개 키 기반 인증 시스템

### PKI - Public Key Infrastructure 이해하기

우리말로 `공개 키 기반 인증 시스템` 으로 이해할 수 있는데, 사실 한 쌍의 키가 중심이 되지만 '공개 키' 라고 불리는 이유는 이 공개 키를 기반으로 암호화 하는 과정이 핵심이라 그런 듯 하다.

공개 키 기반 인증은 한 쌍의 키를 전제로 한다. 만들어 진 한 쌍의 키는 하나는 공개용, 다른 하나는 비공개용 개인 키이다.

#### 공개키 기반 암호화/복호화

PKI 를 통한 통신 과정에 암호화/복호화 작업은 기존에 보내는 사람이 주체가 되어 암호화 하는 과정에 일부 변형이 추가되었다. 암호화된 파일을 받을 대상의 공개 키로 암호화 하는 것이 핵심이다.

인터넷에 있는 많은 참고 이미지 중 이 도식이 가장 좋은 것 같다.

![https://bobbyarvian.blogspot.com/2010/05/pki-public-key-infrastructure-dan-ca.html](/posts/images/pki-brief.jpeg "PKI-public key infrastructure dan CA-certificate authority")

나를 Sender 라 하고 받을 대상을 Recipient 라 보면, 받을 대상의 공개 키로 암호화 하여 파일을 보내면 된다.

내가 누군가에게 암호화된 파일을 받기 위해서 내 공개 키를 제공하면 된다. 인증 관련된 서비스에서 요구하는 공개키가 이런 개념이다.

> 예를 들면, 깃헙의 애플리케이션 연동이나 ssh 서버 연결에 사용되는 public key 가 이런 종류다.

ssh 접속의 경우 내 공개키는 각 클라이언트와 서버가 암호화된 통신을 할 때 사용된다.

이 방식을 응용하여 메일을 교환하거나 송신, 수신되는 네트워크의 데이터들을 암호화 한다.

#### X.509

X.509 는 이 공개 키 기반의 인증 시스템으로 1988년 시작되어 현재는 버전3 를 사용하고 있다. 공개 키 기반의 다른 인증 시스템과 다르게 인증 기관이라는 개념을 도입하여 상위 기관을 통해 인증을 허용하는 시스템을 구성하고 있다.

인증서 작업에 사용되는 파일의 종류를 좀 알아두면 좋다. 대부분 모두 일반 텍스트 파일이다.

- `.key`: 개인 키 파일이다. 이 파일은 공개용이 아니다.
- `.csr`: 인증 기관에 인증서를 받기 위해 요청하는 스펙을 담은 문서
- `.crt`: 인증서 자체, 암호화된 인증서 파일
- `.pem`: Base64 로 인코딩된 인증서로 웹 서버에 사용된다.

#### 브라우저 통신과 SSL/TLS 

웹 서버 데이터에 대한 암호화를 위해 무한에 가까운 수의 서비스가 공개 키를 제공하고 이를 관리하기에는 효율이 좋지 않기에 제3자가 개입하여 인증을 대행하는 방식이다.

CA (Certificate authority) 또는 Root Certificate (루트 인증 기관) 라고 불리는 기관들이 이 역할을 한다.

![https://bobbyarvian.blogspot.com/2010/05/pki-public-key-infrastructure-dan-ca.html](/posts/images/ca-tls-brief.jpeg "PKI-public key infrastructure dan CA-certificate authority")

https 통신에 사용되는 보안 프로토콜로 1999년 SSL(Secure Sockets Layer) 3.0 이 TLS(Transport Sockets Layer) 1.0 으로 공개 되었다.

현재 SSL 버전 사용은 권고 되지 않는다.

## OpenSSL 과 Easy-RSA

OpenSSL 은 이런 암호화/복호화에 관여하는 프로토콜인 TLS 의 오픈소스 구현으로 다양한 시스템에 사용되고 있으며 무료 인증서로 자주 거론되는 Let's encrypt 에서도 사용하고 있다.

얼마 전 (2021년) 3.0 을 공개하였다.

> openssl 툴을 활용해 다양한 인증서 생성 및 인증 요청서를 만들수 있는데, 조금 복잡하다. 그래서 이 과정을 단순화하고 체계적으로 유지하기 위한 'easy-rsa' 가 개발되어 있고, 이를 사용하여 로컬 서비스에 인증서를 적용하는 방법을 알아보려 한다.

아마 대부분의 개발자 시스템에 openssl 은 설치되어 있을 것이다. 추가로 easy-rsa 를 설치해 보자.

```shell
$ brew info easy-rsa
```

실행 파일은 `easyrsa` 이니 헷갈리지 말자.

Public Key Infrastructure(PKI) 환경을 구성하기 위해 초기화 작업이 필요하다.
개인키가 없지 않겠지만 easyrsa 를 통해 서비스용을 하나 만들자.

```shell
$ easyrsa init-pki
```

### macOS 키체인 유틸리티

인증서를 생성하기 전에 인증서를 운영체제에서 사용하고 유지/관리하는 방법을 이해해야 한다.

맥OS 에서 인증서는 `Keychain Access` 유틸리티를 통해 관리할 수 있다. 이 안에 각종 루트 인증서들이 있고 우리가 만들 사설 루트 인증서를 추가하여 관리할 것이다.

![macOS Keychain Access](/posts/images/macos-keychain-access.png)

### 시나리오

주요 작업 시나리오는 다음과 같다.

1. 가상의 최상위 인증 기관 인증서 생성
2. 최상위 인증 기관의 인증서를 운영체제에 추가, 사용 허용
3. 인증 기관에서 상속된 서비스용 인증서 생성
4. nginx 에 인증서 적용

### 루트 인증서 발급

웹 브라우저는 루트 인증기관(CA)을 신뢰하고 대부분의 브라우저는 이 루트 인증서와 암호화된 통신 프로토콜을 내장하고 있다. 

실제 서비스에는 이 루트 인증기관에서 비용을 제공하고 운영할 서비스 사이트의 인증서를 발급 받아 적용해야 한다.

> 우리가 사용할 로컬 개발 환경의 웹 서버를 위해 인증서를 구입하거나 실제 도메인이 부여되지 않은 사이트의 인증서를 발급 받기는 애매하다. 그래서 가상의 루트 인증기관을 만들어 루트 인증서를 생성하고 이 기관을 통해 로컬 서비스의 인증서를 생성해 웹 서버에 적용할 것이다.

#### CA 만들기

easyrsa 를 통해 루트 인증기관의 인증서를 쉽게 생성할 수 있다. 이 과정은 CA 에 대한 조직 정보를 포함한다. easyrsa 는 `Easy-RSA CA` 을 사용한다. 그대로 쓰자.

```shell
$ easyrsa build-ca
```

생성된 인증서는 `/usr/local/etc/pki/ca.crt` 에서 확인할 수 있다.

```shell
$ cat /usr/local/etc/pki/ca.crt
```

이 과정은 다음 내용을 포함하고 있다.

1. 루트 인증기관을 위한 개인 키를 만들고 → `.key` 파일
2. 인증 서명 요청서를 만들고 → `.csr` 파일
3. x509 서명까지 완성한다 → `.crt` 파일

만들어진 루트 인증서 내용은 easyrsa 를 통해 확인할 수 있다. 기본적으로 10년 짜리 서명을 제공해 준다.

```shell
$ easyrsa show-ca
Using SSL: /usr/local/opt/openssl@1.1/bin/openssl OpenSSL 1.1.1m  14 Dec 2021

Showing  details for 'ca'.
This file is stored at:
/usr/local/etc/pki/ca.crt
...
```
#### KeyChain Access 에 추가 및 허용

`File > Import Item` 메뉴를 통해 위에서 생성한 루트 인증서를 추가하고 안전한 사용을 허용하자.

![macOS Keychain Access](/posts/images/macos-keychain-access-permit-root-cert.png)

이 작업 후에는 로그아웃 하고 다시 로그인 하여 시스템에 인증서가 적재되도록 한다.

### 로컬 서비스용 인증서 발급

이제 우리가 사용할 로컬 서비스의 서버 인증서 만들자.

마찬가지로 서버 용 비공개 키를 만들고 루트 인증 기관의 인증을 받아 서버 인증서를 만든다. 복잡한 절차가 있지만 easyrsa 로 단순하게 처리할 수 있다.

이 과정에서 `localhost` 라는 파일 이름 기반의 식별자를 사용하였다. 이 부분을 xxx-server 등으로 자유롭게 변경하여 사용할 수 있다.

#### 멀티 호스트 인증

내 경우 관리하는 호스트가 여러 개이다 보니 멀티 호스트 인증을 하면 하나의 인증서로 다중 도메인을 처리할 수 있다. `EASYRSA_EXTRA_EXTS` 환경 변수에 추가할 도메인 정보를 담아 생성할 수 있다.

```shell
$ export EASYRSA_EXTRA_EXTS="subjectAltName=DNS.1:local.site-a,DNS.2:local.site-b"
```

#### 서버 인증서 생성

```shell
$ easyrsa build-server-full localhost nopass
```

`nopass` 옵션은 인증서의 사용 과정에 패스워드를 적용하지 않는 옵션이다.

> 서버 인증서는 nginx 등의 서버를 구동할 때마다 패스워드를 요구해서 보통 패스워드 없이 생성한다.

이렇게 생성된 인증서는 `/usr/local/etc/pki/issued` 에 저장되어 있다.

앞 부분에는 인증 대상의 정보가 있고 아래에 인증서 정보가 있다.

```shell
$ cat /usr/local/etc/pki/issued/localhost.crt
$ easyrsa show-cert localhost
...
  X509v3 Extended Key Usage:
      TLS Web Server Authentication
  X509v3 Key Usage:
      Digital Signature, Key Encipherment
  X509v3 Subject Alternative Name:
      DNS:local.site-a, DNS:local.site-b, DNS:local.site-c, DNS:local.site-d
...
```

`Subject Alternative Name:` 항목에 다중 도메인이 기록된 것을 확인할 수 있다.

#### nginx 용 인증서 조립

이렇게 생성된 인증서를 웹 서버에 사용하기 위해 조립 과정을 거쳐야 한다.

핵심은 서버 인증서와 루트 인증서를 결합하여 웹 서버에 맞는 인증서 파일을 생성하는 것이다.

```shell
$ cat -p /usr/local/etc/pki/issued/localhost.crt \
         /usr/local/etc/pki/ca.crt > ./server.pem
```

> 오리지널 cat 대신 bat 을 사용하고 있어 `-p` 옵션이 추가되었다.

이렇게 생성된 파일과 서버의 키 정보를 nginx conf 에 추가하자.

```conf
ssl_certificate [server.pem 이 있는 경로]/server.pem;
ssl_certificate_key /usr/local/etc/pki/private/localhost.key;
```

easyrsa 가 없었으면 아래와 여러 단계의 과정을 거쳐야 했을 것이다.

```shell
$ openssl ecparam -out server.key -name prime256v1 -genkey
$ openssl req -new -sha256 -key server.key -out server.csr -config ./openssl-host.conf
$ openssl x509 -req -sha256 -days 365 -in server.csr -CA /usr/local/etc/pki/ca.crt -CAkey /usr/local/etc/pki/private/ca.key -CAcreateserial -out server.crt
$ openssl x509 -in server.crt -text -noout
```

물론 openssl 을 통해 생성된 인증서를 점검할 수 있다.

```shell
$ openssl x509 -in /usr/local/etc/pki/issued/localhost.crt -text -noout
```

![certified localhost in nginx](/posts/images/ngixn-certi-localhost.png)

#### 인증서 관리

`easyrsa` 는 `renew` 나 `revoke` 등의 명령이 제공된다. 

revoke 하더라도 기존 .key 파일이나 .csr 등의 파일을 지워주는 것이 아니니 따로 삭제해줘야 한다. 

도메인 추가 정도라면 EASYRSA_EXTRA_EXTS export 를 수정하고 renew 하면 된다.

