---
title: 자바스크립트 코드 오답 노트
tags: 
- javascript
description: 자바스크립트 코딩시 만나는 문제 중 기록할 만한 것들을 모아 둡니다.
date: 2017-07-01 20:06:38
readingTime: true
author: soomtong
---

## 배열에 레퍼런스가 추가되는 경우 발생할 수 있는 문제

코드를 보기 앞서 문제부터 정리해보자.

> 1 줄의 배열에 인덱스를 통해 짝수와 홀수로 묶어 새로운 배열로 만드는 과정이다. 

올해 초 NetPDL 에디터를 작업할 때 경험했던 문제로 레퍼런스와 카피의 개념을 잊고 코딩했을 때 겪었다.

```javascript
function printList(data = []) {
    let list = []
    let info = {}

    data.map((item, index) => {
        if (!(index % 2)) {
            info.odd = item
        } else {
            info.even = item
            list.push(info)
            info = {}	// here
        }
    })

    return list
}

let data = [
	'element_1',
	'element_2',
	'element_3',
	'element_4'
]

let result = printList(data)

console.log(result)
```

로 출력의 원하는 결과는 다음과 같다.

```json
[ { odd: 'element_1', even: 'element_2' },
  { odd: 'element_3', even: 'element_4' } ]
```

하지만 info = {} 를 초기화 하지 않으면 다음 결과를 보여준다.

```json
[ { odd: 'element_3', even: 'element_4' },
  { odd: 'element_3', even: 'element_4' } ]
```

분명 루프를 돌고 비교를 해 4 줄의 배열을 2 줄의 배열로 줄이고 각 객체를 구성했지만 참조가 잘못된 결과를 나타낸 것이다. `push(info)` 메소드로 추가되는 시점에 `info` 는 레퍼런스이다. 이것이 list 로 들어 갔을 때 값이 들어가는 것이 아닌 것을 기억해야 한다.

```javascript
    list.push({ odd: info.odd, even: info.even })
```

개인적으로 제일 좋은 방법은 위와 같이 새로운 객체를 만드는 것이라고 생각한다.

### 코멘트

커널이나 로우레벨 프로그래밍이 아니더라도 참조는 매우 중요한 주제이다. 물론 프로그래밍 언어가 더 지능적이 될 수록 이런 문제는 줄어들겠지만 개념은 잊지 말아야 할 것이다.

더불어 아래 링크를 정독해 볼 필요가 있다.

### 참고 링크

- http://www.bsidesoft.com/?p=877 (디자인 패턴: 2단 참조)
