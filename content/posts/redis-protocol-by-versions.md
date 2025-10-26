---
title: "Bun 1.3 의 Redis 지원과 RESP"
date: "2025-10-25T09:25:30+09:00"
author: "soomtong"
authorTwitter: "soomtong"
tags: ["redis", "bun", "nodejs"]
keywords: ["bunjs", "ioredis", "node-redis", "resp3"]
description: "Bun 에서 드디어 PG, MySQL, Sqlite 와 함께 내장 Redis 클라이언트가 추가되었다. 이 글에서는 RESP3 프로토콜 중심의 변화와 기존 ioredis, node-redis 와의 차이를 살펴본다."
showFullContent: false
readingTime: true
hideComments: false
---

얼마 전 레디스가 라이선스 문제로 Fork 도 일어나고 이에 대응하는 새로운 솔루션들이 등장했지만, 여전히 레디스는 많은 서비스에서 핵심 데이터 저장소로 사용되고 있습니다. (단순 데이터 저장소 이상의 일을 하고 있습니다.)

레디스 클라이언트 라이브러리들도 지속적으로 발전하고 있으며, Node.js 환경은 `ioredis` 라이브러리가 널리 사용되고 있습니다. Redis 에서 공식적으로 제공하는 node-redis (패키지 이름은 `redis`) 도 있습니다만 아직까지 인지도는 ioredis 가 더 높은 편입니다.
저는 요즘 Node.js 호환 환경을 제공하는 Bun runtime 에 관심을 가지고 있습니다. 최근 1.3 버전에서 Redis 를 직접 지원하는 드라이버를 내장하였습니다. 당연히 고성능이고, 추가 패키지 의존성 없이 바로 사용할 수 있다는 장점이 있습니다.

Redis 호환 라이브러리는 보통 `RESP 지원` 같은 라벨을 달고 있습니다. RESP 는 `Redis Serialization Protocol` 의 약자로, 레디스 서버와 클라이언트 간의 통신에 사용되는 프로토콜입니다. RESP 는 간단하고 효율적인 텍스트 기반 프로토콜로, 다양한 데이터 타입을 지원합니다. 현재 RESP 는 버전 2 와 버전 3 이 존재하며, 각 버전은 약간의 차이가 있습니다.

> Bun 1.3 버전에서 드디어 PG, MySQL, Sqlite 와 함께 내장 Redis 클라이언트가 추가되었다.
> 이 글에서는 RESP3 프로토콜 중심의 변화와 기존 ioredis, node-redis 와의 차이를 살펴본다.

## Bun 1.3, Redis 내장 지원

Bun 은 Node.js 와 호환되는 고성능 런타임으로, 최근 1.3 릴리스에서 **Redis 클라이언트**를 내장했다.
기존에는 `ioredis` 나 `node-redis` 같은 외부 패키지를 설치해야 했지만, 이제 Bun 환경에서는 바로 사용할 수 있다.

```js
import { connect } from "bun:redis";
const redis = connect({ hostname: "127.0.0.1", port: 6379 });
await redis.set("key1", "value1");
console.log(await redis.get("key1"));
```

> Bun 의 Redis 클라이언트는 Valkey(Redis의 오픈 포크)와의 호환성도 고려되어 있으며,
기본적으로 RESP3 (Redis Serialization Protocol v3) 를 사용한다.

## RESP3 — Redis 프로토콜의 세 번째 세대

기존 Redis 5 까지는 RESP2 프로토콜을 사용했다.
RESP2 는 단순하지만 모든 데이터를 문자열 또는 배열로 표현한다.
이 방식은 구현이 단순한 대신 자료형 구분이 불분명하다는 단점이 있었다.

RESP3 는 Redis 6.0 부터 도입되어,
이제는 Map, Set, Push, Attribute 등 다양한 타입을 명확히 구분할 수 있다.
이 덕분에 클라이언트는 Redis 서버의 데이터 구조를 더 명확하게 이해할 수 있게 되었다.

redis-cli 에서 다음 명령을 수행하는 과정에서 해당 데이터 구조를 확인할 수 있다.

```redis
localhost:6379> sadd tags "redis" "database" "nosql"
(integer) 3
localhost:6379> smembers tags
1~ "redis"
2~ "database"
3~ "nosql"
```

`1~` 처럼 RESP3 에서는 Set 타입을 명확히 구분하여 표현한다.

반면 RESP2 에서는 다음과 같이 단순 배열로 표현된다.

```redis
10.89.224.3:6379> sadd tags "redis" "database" "nosql"
(integer) 3
10.89.224.3:6379> smembers tags
1) "database"
2) "nosql"
3) "redis"
```

### 프로토콜 협상

- **RESP2**: 기본 프로토콜, 별도 협상 불필요
- **RESP3**: `HELLO` 명령어를 통한 프로토콜 협상 필요
  - Redis v5 이하에서는 `HELLO` 명령어를 지원하지 않아 오류 발생
  - Redis v6+ 에서만 RESP3 프로토콜 사용 가능

### redis-cli 에서 확인

> redis 명령은 대소문자 구분이 없으므로 `hello`, `HELLO`, `HeLLo` 모두 동일하게 동작한다.

```bash
$ redis-cli -h localhost -p 6379 -a your_password
```

```redis
localhost:6379> PING
PONG

localhost:6379> HELLO
1) "server"
2) "redis"
3) "version"
4) "8.2.2"
5) "proto"
6) (integer) 2  # RESP2 세션
...
```

`proto` 필드가 2 로 표시된다면 RESP2 세션이다.

```redis
localhost:6379> HELLO 3
1# "server" => "redis"
2# "version" => "8.2.2"
3# "proto" => (integer) 3   # RESP3 세션
4# "id" => (integer) 7
5# "mode" => "standalone"
...
```

`proto` 필드가 3 으로 표시된다면 RESP3 세션이다. 반환 결과에서도 볼 수 있듯 Map(Dictionary) 형태로 응답이 온다.

#### 구버전 redis 에서 확인하기

```redis
10.89.224.3:6379> PING
PONG

10.89.224.3:6379> HELLO
(error) ERR unknown command `hello`, with args beginning with:
```

일단 HELLO 명령어가 없다는 점에서 RESP3 를 지원하지 않는 구버전임을 알 수 있다.

```redis
10.89.224.3:6379> INFO
# Server
redis_version:5.0.14
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:6159119d86cfc813
redis_mode:standalone
os:Linux 6.1.112+ x86_64
arch_bits:64
multiplexing_api:epoll
atomicvar_api:atomic-builtin
gcc_version:10.2.1
process_id:2639
run_id:8465853894049bb1b20c67fee4e72700adb6880b
tcp_port:6379
uptime_in_seconds:20775231
uptime_in_days:240
hz:10
configured_hz:10
lru_clock:16542474
executable:/data/redis-server
config_file:/usr/local/etc/redis/redis.conf
```

## node-redis, ioredis 그리고 Bun

Node.js 생태계에는 오랫동안 두 가지 주요 Redis 클라이언트가 사용되고 있다.

| 구분 | node-redis | ioredis |
| --- | --- | --- |
| 유지보수 주체 | Redis 공식팀 | 커뮤니티 |
| 프로토콜 | RESP3 (기본) | RESP2 |
| Redis 버전 호환성 | 6 이상 | 2.6 이상 |
| Cluster 지원 | 지원 | 지원 (안정적) |
| Sentinel 지원 | 지원 | 지원 |
| TypeScript 지원 | 우수 | 양호 |
| 성향 | 최신 프로토콜, 단일 연결 중심 | 범용성, 안정성 중심 |

Bun 의 내장 클라이언트는 구조적으로 node-redis 와 유사하다.
둘 다 RESP3 중심 설계, async/await API, Redis 6+ 호환이라는 점이 같다.
반면 ioredis 는 RESP2 기반으로 오래된 버전과의 호환성을 중시한다.
겉보기엔 동일하지만, node-redis 는 RESP3 기반으로 더 많은 자료형을 구분해 처리한다.
예를 들어 SMEMBERS 는 단순 배열이 아니라 Set 으로 표현된다.

하지만 Node 클라이언트 패키지에서는 이 모든 것이 호환성 유지 목적 때문에 기본 객체와 배열로 변환되어 제공된다. 따라서 실제 사용 시에는 큰 차이를 느끼기 어렵다.

그러나 예상하기로는 RESP3 의 자료형 구분 덕분에 클라이언트 내부 구현이 더 명확해지고, 향후 새로운 자료형 지원이 용이하다는 장점이 있을 것이다.

### Bun Redis 클라이언트의 제약

현재 (v1.3 기준) Bun 의 Redis 클라이언트는 다음 기능이 제한적이다.
- Redis Cluster / Sentinel 지원 미완성
- 명령 수 제한 (약 60여 개의 명령만 구현됨)
- Pipeline, Pub/Sub 일부 지원

그러나 Bun 팀은 이를 점진적으로 완성할 계획이며, Valkey 호환성 확보와 RESP3 확장은 이미 완료된 상태다. bun/src 를 통해 확인한 내부 코드를 보면, 모듈 이름도 `valkey` 로 되어 있다.

- 소스코드 [src/valkey/valkey_protocol.zig](https://github.com/oven-sh/bun/blob/main/src/valkey/valkey_protocol.zig)

---

최근에 오래된 서비스의 패키지 갱신 작업을 하고 있는데 버전을 무작정 올리는 것이 능사가 아님을 다시 한번 깨닫고 있습니다. 실 예로 제가 일하고 있는 회사의 인프라는 레디스 버전 5 를 사용하고 있는 중입니다. 고성능 패키지라고 그냥 바꾸면 안 되는 거죠. 특히 RESP 프로토콜이 버전이 달라졌다는 것은 어떤 경우에는 브레이킹 체인지가 발생할 수 있고 어떤 경우에는 호환 모드로 인해 성능 저하를 감수해야 할 수 있습니다. (물론 이 정도 수준의 패키지들은 호환성을 잘 맞추고 출시하겠지만요.)

하지만 운영 중인 코드의 버전을 너무 오래 방치해 두면 언젠간 그 뒷감당을 해야 합니다. 사실 런타임 버전을 올려서 성능 이득을 얻어가는 경우가 종종 있습니다. 나은 성능은 가용 예산을 확보할 수 있는 것이구요. 어떤 패키지는 보안 문제나 유지 관리의 목적으로 최소 버전을 강제하기도 하여 반드시 업그레이드가 필요한 경우가 발생합니다. 여유가 되는 대로 꾸준히 관심을 가지고 지켜보면서 대응하는 것이 중요합니다. Bun 의 Redis 지원도 그런 맥락에서 고려할 부분이 있습니다. 다만, Bun 이 점점 더 성숙해지고 있는 것을 보면 모든 것을 Bun 에서 해결할 수 있는 날이 머지않아 올 것 같습니다.

- Use builtin [src/init/rule.md](https://github.com/oven-sh/bun/blob/main/src/init/rule.md)
