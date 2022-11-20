---
title: Elastic Stack 101
tags:
- elastic search
description: 엘라스틱 서치의 ELK 스택 설치 기록
readingTime: true
date: 2017-05-10 22:41:05
author: soomtong
---

우선 등장하는 몇가지 용어에 대해 점검해보고 넘어가자. 일래스틱, 엘라스틱 등으로 불린다. (동료들의 의견에 따라 `엘라스틱`으로 칭한다.)

## 최근 뉴스 및 개요

- 2017년 4월 5.3 버전이 릴리즈 되었습니다.
- 2017년 5월 5.4 버전이 릴리즈 되었습니다.
- 2017년 5월 부터 6.0 버전의 알파 테스트가 시작되었습니다.

국내 서적 『시작하세요! 엘라스틱서치』 예제 코드 저장소

- https://github.com/wikibook/elasticsearch

### 엘라스틱 서치

엘라스틱 서치는 아파치 루신으로 개발된 분산 검색엔진

- JSON 기반
- RESTFul API 지원
- 국내에서는 KTH, 위메프 등에서 사용 중

검색 뿐 아니라 집계 기능이 유명해 실시간 분석 엔진으로도 자주 사용된다.

### 루신

> Apache Lucene

자바로 작성된 풀 텍스트 검색엔진. 아파치 솔라, 인덱스탱크, 엘라스틱서치 같은 솔루션들의 기반 라이브러리.

### 로그스태시

데이터 로그 파이프라인

### 키바나

시각화 처리 애플리케이션

## 엘라스틱 스택

**Elastic Stack** (초기에는 **ELK STACK** 으로 불렸다.)

엘라스틱서치 + 로그스태시 + 키바나의 조합

> 로그스태시에서 수집된 데이터로그를 인덱싱하고 엘라스틱서치에서 필터링 한 후 키바나에서 비주얼라이즈 한다.

가 기본 컨셉

### 국내 사례

NHN 엔터 두레이

- https://dooray.com/join

그리고 인하우스 웨어로 간간히 사용되고 있는 분위기

### 튜토리얼

5.0 버전을 기준으로 작성되었고 한글 영상으로 볼만 할 것 같다.

- https://www.inflearn.com/course/elk-스택-데이터-분석/
- https://www.youtube.com/playlist?list=PLVNY1HnUlO24LCsgOxR_eK2Yi4sOgH9Pg

### 라이선스 및 개발 언어

모두 공히 아파치2 라이센스를 따르고 있다.
안전한 빌드를 위해서는 JDK 8 과 JRuby 1.7 이상을 필요로 한다.

엘라스틱서치는 자바, 로그스태시는 루비(jRuby), 키바나는 앵귤러1 을 기반으로 하고 있다.


## 설치 및 구동

### 도커를 사용해 쉽게 가보자

우선 도커 세팅이 끝나면 호스트 환경에서 손봐야 할 것이 있다.

> VM.MAX_MAP_COUNT 를 늘려주세요

```
$ grep vm.max_map_count /etc/sysctl.conf
vm.max_map_count=262144
```

그리고 반드시 시스템 리부팅 해야 반영 된다. (당연하지)

#### 도커 컨테이너 관련 명령

> docker ps
> docker ps -a

> docker rm [container_id]
> docker rm `docker ps -a -q`

#### 도커 이미지 관련 명령

> docker images
> docker rmi [image_id]
> docker rmi -f [image_id]

#### 도커 컴포저 관련 명령

> docker-compose up
> docker-compose up --build

#### ELK 스택 도커 컴포즈 파일 클론

- https://github.com/deviantony/docker-elk

#### 다운로드 하면서 빌드

그냥 UP 하면 중간에 WARNING 이 나온다. --BUILD 옵션을 붙이자.

> sudo docker-compose up --build

필요한 이미지 다운로드가 끝나면 자동으로 도커 컨테이너가 등록된다. 

- http://localhost:9200
- http://localhost:5601

을 통해 정상 작동을 확인한다.


### 로컬 피시에 패키지를 통해 인스톨을 해보자

엘라스틱 스택은 현재 5.2.2 (2/28/2017) 버전이 최신 버전인 상태로 이 시점의 가이드에 따라 설치해 본다.

~~환경은 우분투 1604 환경에서 이루어지고 데비안 패키지를 따라감.~~
(데비안 패키지 매니저는 문제가 있는지 systemd 스크립트가 upstart 스크립트를 변환해 작성된 듯 하고 세팅이 깔끔하게 마무리 되지 않아 그냥 바이너리 설치를 추천함.)

#### 본격 설치 전 수행 사항

패키지 설치를 위한 퍼블릭 키를 추가합니다. 가급적 bash 나 zsh 환경에서 진행하는 편이 스크립트 실행에 알수 없는 오류를 줄 일 수 있다.

> wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

> sudo apt-get install apt-transport-https

> echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list

데비안 배포본에 따라 `add-apt-repository` 를 통해 추가되는 소스 레포지토리의 설치 경로가 다른 경우가 있어서 위 방법을 선호한다고 한다.

> sudo apt update

#### 엘라스틱 서치 설치

> sudo apt install elasticsearch

설치를 마치고 systemd 등록도 자동으로 처리 된다. 설치 시간은 SSD 가 설치된 인텔 Core I5 16G Ram 환경에서 6~7 초 정도 걸렸다.

> sudo systemctl status elasticsearch.service

명령을 통해 서비스 데몬이 잘 등록되었는지 확인한다. 잘 동작한 것처럼 보이지만 데몬은 실행되지 않았고 리부팅 등의 기본 과정을 거친 후에도 작동이 되지 않았다.

설치된 경로의 실행파일을 통해서는 정상적으로 실행이 된다. 스크립트의 퍼미션 같은 것들이 문제일 것 같은데 조금 더 살펴 보다 포기했다.

### 로컬 피시에 바이너리 파일 설치로 진행해보자

압축되어 제공되는 실행파일을 별도의 경로에 풀어 실행하기로 계획을 변경했다. 어짜피 소스코드를 수정해야 하는 일이 주어질 확률이 높기 때문에 이 경우가 오히려 더 나은 방법이 될 수 있다.

우선 JDK 를 설치한다. (사실 JRE 만 있어도 작동한다.)

> sudo apt install openjdk-8-jdk-headless

#### 엘라스틱 서치 설치

다운로드한 파일을 작업 폴더에 풀고 해당 경로로 이동한다. fish 쉘 사용자는 아래와 같이 실행한다.

> bin/elasticsearch -Epath.conf={$pwd}/config/

bash 나 zsh 라면

> bin/elasticsearch -Epath.conf=$(pwd)/config/

정상 작동의 느낌이 팍 난다.

> curl -XGET 'localhost:9200/?pretty'

정상 구동된 것을 확인할 수 있다.

#### 키바나 설치

마찬가지로 파일을 풀고 이동하고 설정파일을 걸고 실행한다.

설정 파일은 `loging.dest: stdout` 정도만 풀어주고 실행해도 좋다.

> bin/kibana -c config/kibana.yml

디폴트 5601 포트로 실행된다. 이 부분은 나중에 엔진엑스를 통해 reverse-proxy 처리한다.

브라우저를 열어 5601 포트로 접속해 웹 애플리케이션을 구경할 수 있다.

##### 프로덕션 세팅

SSL 이나 로드밸런싱 등의 내용이 설명되고 있다.

- https://www.elastic.co/guide/en/kibana/5.2/production.html

#### 로그스태시 설치

마찬가지로 파일을 풀고 이동하고 설정파일을 걸고 실행한다.

> bin/logstash -f ../docker-elk/logstash/pipeline/logstash.conf

로그스태시의 경우 특정한 .conf 파일이 함께 포함되어 있지 않다. 특정 로그 형태를 파싱하는 역할을 하기 때문인데 이에 대한 예제들이 문서 페이지에 제공되고 있다.

- https://www.elastic.co/guide/en/logstash/current/config-examples.html
- https://www.digitalocean.com/community/tutorials/adding-logstash-filters-to-improve-centralized-logging-logstash-forwarder

또는 위 경우처럼 도커에서 사용한 설정 파일을 지정해 임시로 실행시킬 수 있다.


## 샘플 데이터 사용

깃헙 엘라스틱 레포지토리에는 데모에 사용할 로그 데이터들이 잔뜩 있다. 이걸로 테스트를 조금 더 진행할 수 있다.

- https://github.com/elastic/examples

### 도커 환경에서 적용해보기

디폴트 세팅의 로그 스태시에는 몇 줄의 세팅 내용 뿐이다.

```
input {
    tcp {
        port => 5000
    }
}

## Add your filters / logstash plugins configuration here

output {
    elasticsearch {
        hosts => "elasticsearch:9200"
    }
}
```

그런데 샘플 폴더의 `logstash.conf` 는 디테일한 내용이 더 들어가 있다.

이 파일을 수정해 사용할 수도 있지만 우선 도커 프로세스를 사용해 처리해보자.

우선 도커엘크 리드미에 있는 내용부터 해보자.

```
ElasticStack_NGINX-json $ nc localhost 5000 < nginx_json_logs
```

로그가 보내지긴 했겠지만 정상 처리되지 않을 것이다. 필터가 정의되어 있지 않기 때문이다.

#### 컨피규레이션 

`nginx_json_logstash.conf`, `ngix_json_template.json` 파일을 참조해 `pipeline` 에 있는 `logstash.conf` 파일에 필터 내용을 추가하고 템플릿 파일 참조를 위해 `docker-compose.yml` 파일도 수정한 후 도커-컴포즈를 다시 구동한다.

이 경우 `logstash: volunmes: [template]` 매핑이 추가되었다.

```
version: '2'

services:

  elasticsearch:
    build: elasticsearch/
    ports:
      - "9200:9200"
      - "9300:9300"
    environment:
      ES_JAVA_OPTS: "-Xmx256m -Xms256m"
      xpack.security.enabled: "false"
      xpack.monitoring.enabled: "false"
      xpack.graph.enabled: "false"
      xpack.watcher.enabled: "false"
    networks:
      - elk

  logstash:
    build: logstash/
    volumes:
      - ./logstash/config/logstash.yml:/usr/share/logstash/config/logstash.yml
      - ./logstash/pipeline:/usr/share/logstash/pipeline
      - ./logstash/template:/usr/share/logstash/template
    ports:
      - "5000:5000"
    environment:
      LS_JAVA_OPTS: "-Xmx256m -Xms256m"
    networks:
      - elk
    depends_on:
      - elasticsearch

  kibana:
    build: kibana/
    volumes:
      - ./kibana/config/:/usr/share/kibana/config
    ports:
      - "5601:5601"
    networks:
      - elk
    depends_on:
      - elasticsearch

networks:

  elk:
    driver: bridge
```

```
input {
    tcp {
        port => 5000
    }
}

## Add your filters / logstash plugins configuration here
filter {
  date {
    match => ["time", "dd/MMM/YYYY:HH:mm:ss Z" ]
    locale => en
  }
  geoip {
    source => "remote_ip"
    target => "geoip"
  }
  useragent {
    source => "agent"
    target => "user_agent"
  }
  grok {
    match => [ "request" , "%{WORD:request_action} %{DATA:request1} HTTP/%{NUMBER:http_version}" ]
  }
}

output {
    elasticsearch {
        hosts => "elasticsearch:9200"
        index => "nginx_json_elastic_stack_example"
        document_type => "logs"
        template => "/usr/share/logstash/template/nginx_json_template.json"
        template_name => "nginx_json_elastic_stack_example"
        template_overwrite => true
    }
}
```

#### 로그 데이터 추가하기

로그스태시를 포함한 서비스가 정상적으로 올라오는지 확인한다.

그리고 다시 로그를 nc 를 통해 추가한다.

```
nc localhost 5000 < nginx_json_logs
```

브라우저에서 데이터가 잘 들어갔는지 확인한다.

- http://localhost:9200/nginx_json_elastic_stack_example/_count

#### 인덱스 정의

브라우저를 열어 키바나에 접속한다.

매니지먼트 탭에서 인덱스 패턴을 등록한다.

`index name or pattern` 필드에 `nginx_json_elastic_stack_example` 라고 입력한다.

타임필드 네임은 `@timestamp` 가 지정된 것을 확인 한 후 Create 버튼을 누른다.


#### 대시보드 프로파일 로드

미리 세팅된 대시보드 프로파일을 로드해보자.

`Saved Object` 탭우측에 있는 `Import` 버튼으로 정의된 프로파일을 불러들인다. 대시보드 정의 파일에 문제가 있는지 5.2.2 버전에서 정상적으로 처리되지 않는다. 

```
Error: Could not locate that index-pattern-field (id: response)
```

이란 메시지가 나옴. 대시보드는 다시 정의하면 되는 문제니까 이 정도 확인하고 실제 데이터를 리뷰해 보자.

#### 디스커버리

디스커버 메뉴 인풋 필드에서 
```
host:172.19.0.1
```
라고 입력하면 해당 로그가 쿼리된다.


### 일반 환경에서 샘플 데이터 사용해보기

샘플 데이터가 제시하는 가이드를 도커 환경에서 따라하기엔 아직 도커 사용 능력이 부족하기 때문에 별도 실행과정을 통해 로그스태시 데이터를 추가하고 대시보드를 구경해보자.

#### 서비스 구동

엘라스틱, 키바나(얘는 대충 실행해도 된다)를 올려두고, 샘플 데이터 폴더에 들어가 설정 파일들을 한 번 살펴 본다. 인풋, 필터, 아웃풋 형태에 익숙해지도록 해 보자.

템플릿 경로를 확인하고 시작해보자.

```
cat nginx_logs | ../../logstash-5.2.2/bin/logstash -f nginx_logstash.conf
```

로그가 콘솔에서 정상적으로 입력되는 화면을 본다면 성공적으로 처리된 것이다.

마찬가지로 브라우저에서 레코드가 정상적으로 추가되었는지 확인한다.

- http://localhost:9200/nginx_elastic_stack_example/_count

#### 키바나 인덱스와 대시보드

매니지먼트 메뉴에서 인덱스 패턴 탭으로 이동한 후 `Index name or pattern` 항목에 `nginx_elastic_stack_example` 를 입력한다. 이 내용은 해당 로그 컨피규레이션 파일에 기록된 ID 스트링이다.

이어서 대쉬보드 레이아웃이 담긴 Saved Objects 탭에서 외부 파일을 임포트 한다.

## 새비어스 옴니픽 연동

> 네트워크 장비 업체인 징코스에서 새피어스 옴니픽이라는 패킷 캡쳐 및 패킷 분석 소프트웨어를 사용하고 있다. 옴니픽 장비는 패킷 분석 데이터를 파일로 저장하고 있는데 이 실시간 분석 데이터를 엘라스틱 스택과 연동하여 키바나의 대시보드로 표시하는 작업을 진행하려 한다.

### 옴니픽 설치 및 구동

트라이얼 버전은 윈도우즈 환경에만 제공된다.

호스트 머신에 버추얼박스를 설치하고 윈도우즈 10 을 설치한다. (윈도우즈 라이센스가 없을 경우 3개월 짜리 Edge 브라우저 개발자용 버전으로 대충 진행할 수 있다.) 새비어스 홈페이지에서 트라이얼 라이센스 버전을 다운로드 해 설치하고 분석 데이터를 저장할 폴더를 호스트의 저장소로 지정한다.

옴니픽은 패킷 캡처를 위한 서비스 데몬과 캡쳐 컨트롤과 통계 분석을 위한 애플리케이션으로 구성된다. (둘 다 설치해야한다.)

버추얼 박스의 폴더 공유 기능을 사용해 로그스태시가 참조할 수 있는 위치로 분석 데이터가 저장되도록 준비해 두고 패킷 캡쳐를 시작한다.

리눅스 호스트 머신에서 wireshark 를 통해 캡쳐된 데이터를 옴니픽에서 사용할 수 있도록 테스트 했지만 알 수 없는 이유로 단 한 번 파일이 정상적으로 불러 들여졌으나 이후 다양한 방법을 통해 wireshark 캡쳐 데이터를 사용하려 했지만 실패했다.

#### 옴니픽 분석 데이터 저장 옵션

이 테스트의 경우 `Capture Options` 패널에서 `General` 항목의 `Capture to disk` 는 필요하지 않다. (이 파일을 wireshark 에서 사용할 수 있는지는 아직 확인하지 않았다.)

`Statistics Output` 패널에서 1분 단위로 통계 파일을 저장하고 `Report folder` 부분을 공유된 호스트 폴더로 지정한다.

### 옴니픽용 로그스태시 컨피규레이션

디폴트로 제공되는 세팅 파일은 다음과 같다.

- https://gist.github.com/soomtong/71b304afbe505b11c46574f0188883ac

각 항목에서 중요한 부분은 `file` 항목에 있는 `path` 의 폴더 참조, 파일 참조와 `type` 의 항목이 `filter` 에 사용되는 식별값이다.

필터 영역에서 사용되는 식들은 쭉 살펴보면 무리가 없고 간혹 참조되는 필터 파일과 위치 정보 매핑용 파일만 주의해서 변경해주면 된다.

```
input {
    file {
        path => "/home/soomtong/Repository/elastic-stack/omnipeek/**/Summary Statistics.csv"

        ...

filter {
    if [type] == "sv_urls" {
      
        ...
        
        grok {
                patterns_dir => ["/home/soomtong/Repository/elastic-stack/logstash-conf"]
                patterns_files_glob => ["savvius_patterns.txt"]
                match => [ "Message", "^%{MY_URI:url}?(?:%{URIPARAM})? from %{IP:ip_address}" ]
        }
       
        ...
```

### 로컬 호스트의 엘라스틱 스택 실행 스크립트

로컬에 깔린 엘라스틱 스택의 구성 환경은 다음과 같다.

```
soomtong@Gurum-H87N-WIFI~/R/elastic-stack> ll
drwxr-xr-x  9 soomtong soomtong 4.0K Apr  4 09:04 elasticsearch-5.3.0/
drwxrwxr-x 12 soomtong soomtong 4.0K Mar 23 12:46 kibana-5.3.0/
drwxr-xr-x 12 soomtong soomtong 4.0K Apr  4 11:08 logstash-5.3.0/
drwxr-xr-x  2 soomtong soomtong 4.0K Apr  5 16:27 logstash-conf/
drwxr-xr-x 78 soomtong soomtong 4.0K Apr  6 17:30 omnipeek/
-rwxr-xr-x  1 soomtong soomtong   79 Apr  4 17:21 run-elasticsearch.sh*
-rwxr-xr-x  1 soomtong soomtong   60 Apr  4 17:22 run-kibana.sh*
-rwxr-xr-x  1 soomtong soomtong   71 Apr  5 16:28 run-logstash.sh*
```

각각의 실행 파일은 별도의 스크립트로 빼 두었다.

```
$ cat {run-elasticsearch.sh,run-kibana.sh,run-logstash.sh}
./elasticsearch-5.3.0/bin/elasticsearch -Epath.conf=elasticsearch-5.3.0/config
./kibana-5.3.0/bin/kibana -c kibana-5.3.0/config/kibana.yml
./logstash-5.3.0/bin/logstash -f logstash-conf/savvius-gingkos.conf -r
```

로그스태시의 컨피규레이션 파일 뒤의 `-r` 옵션은 `--config.reload.automatic` 과 같은 역할을 한다. 그 기능은 문자 그대로 컨피규레이션 파일이 변경되면 로그스태시가 다시 구동된다.

옴니픽의 통계 데이터와 로그스태시 필터 설정 파일이 정상적이라면 데이터가 추가될 때마다 JSON 형태의 콘솔 로그를 뿌릴 것이다.

### 키바나에서 확인하기

로그스태시에서 작업된 데이터는 키바나에서 확인할 수 있다.

키바나의 데브툴즈 메뉴에서 콘솔에 REST + Graph Object 스타일의 명령을 입력하면 엘라스틱서치에 저장된 데이터를 쿼리할 수 있다.

녹색 화살표를 클릭해도 되지만 컨트롤+엔터키(맥에서는 이와 동등한 역할을 하는)를 눌러 데이터를 확인할 수 있다.

```
GET _search
{
  "query": {
    "match_all": {}
  }
}

GET _cat/indices

DELETE _all
```

두 번째와 세 번째 명령 정도면 데이터가 정상적으로 스택되고 있는지 확인할 수 있다. 어시스트 기능이 있어 어떤 명령을 수행할 수 있는지 개략적으로 파악할 수 있다.

#### 대시보드 리프레시 관련

대시보드를 구성하다가 미처 확인하지 못하고 당황했던 부분이 있다.

리얼타임으로 추가되는 데이터인데 키바나에서는 리얼타임으로 피드백을 반영하지 못한다는 점이었다. 

우선 매니지먼트 메뉴에서 `Saved Object` 탭에서 저장된 대시보드 템플릿에 대해 'refreshInterval' 을 지정하는 부분이 있어 변경하였으나 정상 반영되지 않았다.

다음으로 `Advenced Settings` 에서 'refreshInterval' 에 대한 항목을 찾을 수 있었다. 이 역시 즉시 반응하지 않았다. 웹 페이지를 다시 로딩을 해도  마찬가지.

어처구니 없게도 해당 항목을 조절하는 부분은 화면 상단 우측에 있었다.

대시보드 템플릿 추가/저장/공유 등의 옵션과 함께 리프레시 인터벌과 리프레시 On/Off 토글 컨트롤이 있다. 그리고 그 옆에는 흔히 보는 타임 레인지 컨트롤이 있다.

### 첫 번째 대시보드 구성

필터 처리된 파라메터들을 쿼리해 그래프를 그리는 작업을 반복하다보면 뭔가 나올 것이다.

> 으쌰! 만들어보자! 음, 잘 안되네... (하지만 나는 여기까지만 하기로)

## 사용 소감

하루 정도 사용해 본 후 소감을 정리하자면 엘라스틱스택은 로그를 분석하는 분야에 매우 매력적인 솔루션임에 분명하다는 것이다.

- 실시간 패턴 분석은 유의미하다.
- 로그를 시각화 하는 것은 중요한 기능이다.
- 제품 자체로 이슈메이커가 된다.
- 패킷엔진에 패키징되면 좋겠다.

개발 부분에서는 로그스태시를 잘 활용하는 것이 포인트가 될 것이다. 로그스태시를 잘 활용하려면 필터를 잘 활용하는 것이 될 것이고 필터를 잘 활용하려면 GROK 패턴에 익숙해야 한다.

> 엘라스틱스택 -> 로그스태시 -> 필터 -> GROK 패턴 매칭

대시보드를 잘 활용하는 부분은 개발 부분이 아니라 운영이나 관리자 레벨에서 담당해야 하는 부분이다. 적절히 익히고 교육할 정도만 되면 충분하다.

### 참고

- https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html
- https://www.elastic.co/guide/en/elasticsearch/reference/master/index.html
- https://www.elastic.co/guide/en/kibana/master/index.html
- https://www.elastic.co/guide/en/logstash/master/index.html
- https://www.elastic.co/guide/en/x-pack/current/index.html
- http://grokconstructor.appspot.com/do/match
