---
title: Write Deadly Simple Compiler Part 3
slug: write-simple-compiler-3
date: 2023-01-07T20:00:00+09:00 
author: soomtong 
tags: 
  - compiler 
  - assembly
  - javascript 
keywords: 
  - compiler
  - parser
  - asm
  - llvm
description: "내가 처음으로 구독한 개인 메일인 Phil 의 웹 페이지에 있는 2018년 말 포스트인 'Writing a lisp compiler from scratch in JavaScript' 를 학습하며 기록한 글이다."
showFullContent: false
readingTime: true
---

> [Phil](https://notes.eatonphil.com) 의 'Writing a lisp compiler from scratch in JavaScript' 첫 포스트의 세 번째 파트인데 분량이 많아 새 포스트를 열었다.

지난 포스트에 이어서... 

Phil 의 가이드에 마지막 부분은 파싱된 배열을 어셈블리로 변환하는 컴파일 과정을 담고 있다.
그리고 어셈블리를 어셈블! 하는 것으로 첫 포스트를 마치고 있다.

## 테스트 코드 러너

주어진 구문을 파싱하고 컴파일 하는 간단한 스크립트를 하나 만들어 보자. 보통 script runner 같은 이름이 사용된다.

```ts
const { compile, parse } = require('./lib');

const program = '(+ 3 (+ 1 2)';
const [parsed] = parse(program);
const assmbled = compile(parsed[0]);

console.log('---------------------------------------------------');
console.dir(parsed, { colors: true, depth: 5, showHidden: false });
console.log('---------------------------------------------------');
console.log(assmbled);
```

이 스크립트를 실행하면 아래와 같은 결과를 얻을 수 있다.
```shell
---------------------------------------------------
[ [ '+', 3, [ '+', 1, 2 ] ] ]
---------------------------------------------------
	.global _main_
	.text
plus:
	ADD RDI, RSI
	MOV RAX, RDI
	RET
_main_:
	PUSH RDI
	PUSH RSI
	MOV RDI, 3
	PUSH RDI
	PUSH RSI
	MOV RDI, 1
	MOV RSI, 2
	CALL plus
	POP RSI
	POP RDI
	MOV RSI, RAX
	CALL plus
	POP RSI
	POP RDI
	MOV RDI, RAX
	MOV RAX, 0x2000001
	SYSCALL
```

파싱된 AST 의 첫 원소가 우리가 사용할 결과이다. macOS 에서 컴파일하기 위해 추가한 코드는 저장소를 살펴 보도록 한다.

대략 개요는 아래와 같다.

```ts
export const ExitCode: Record<string, string> = {
  darwin: '0x2000001',
  default: '60',
};
```

이제 진짜 어셈블 해 실행 파일(바이너리)를 만들어 보자.

## 실행 코드 생성

테스트 코드 러너를 Phil 의 문서와 같이 `ulisp` 이란 파일로 만들어 실행 가능하게 하고 `gcc` 명령도 함께 실행되도록 하면 이 전체 동작을 포장할 수 있을 것이다.

`ulisp` 파일은 실행 권한을 부여하여 `node` 가 있으면 스스로 실행 가능하도록 하고 `./tmp` 폴더에서 컴파일하고 어셈블하고 실행 파일을 만드는, 소위 툴체인 형식을 가지도록 해보자.

소스를 지정하고 실행 파일을 만들어 내는 절차를 좀 그럴 듯 하게 가져가 보려 했지만 어짜피 한 줄의 S-Expression 구문을 컴파일하는 것이 전부인 관계로 그냥 아래와 같은 형식으로 명령을 실행하도록 하자.

```shell
$ ulisp.js '(+ 3 (+ 2 1))'
```

> 이 과정을 겪으며 알게된 사실인데 우리 프로그램의 엔트리 포인트로 사용하는 `_main_` 은 잘못된 식별자였다. 정상 동작을 위해 `_main` 으로 변경하였다.

어셈블리를 컴파일하기 위해 `gcc` 를 사용하고 있다. gcc 는 gnu compiler collection 의 약자로 다양한 코드를 컴파일 할 수 있다. 어셈블리 컴파일(어셈블)하기 위해 아래와 같은 명령을 사용한다.

`gcc 옵션 -o 출력파일이름 소스파일이름`

ulisp.js 의 전체 코드는 아래와 같다.

```js
#!/usr/bin/env node
const { writeFileSync } = require('fs');
const { execSync } = require('child_process');

const { parse, compile } = require('./lib');

const CC = 'gcc';
const SOURCE = 'program.S';

function main(args) {
  const script = args[2];

  if (!script) {
    console.log('no source');

    return;
  }

  const program = compile(parse(script)[0][0]);

  writeFileSync(SOURCE, program);

  execSync(`${CC} -mstackrealign -masm=intel -o program ${SOURCE}`);

  console.log('done; \nrun ./program and echo $? for output');
}

main(process.argv);
```

실행 플래그를 주는 방법은 `chmod +x ulisp.js` 이다. 마지막 로그를 보면 알겠지만 우리 프로그램은 그냥 종료할 뿐이고 화면에 출력하는 시스템 콜은 없다.

```shell
$ ./ulisp.js '(+ 3 (+ 2 1))'
done;
run ./program and echo $? for output

$ file program # or use hexyl to see hex binary
program: Mach-O 64-bit executable x86_64

$ ./program

$ echo $?
6
```

이렇게 첫 포스트가 마무리 되었다. 이어지는 내용은 좀 더 그럴듯한 프로그램을 위해 함수를 선언하고 이를 사용하는 프로그램 코드를 컴파일하는 과정을 담고 있다.

## 서브 루틴

보통 함수나 프로시져를 서브루틴의 일부로 본다. 서브루틴은 반복되는 코드를 재사용하기 위한 방법으로 반환하는 값이 있으면 보통 함수, 반환하는 값이 없는 경우는 프로시져로 불렸는데 지금은 크게 구분하여 사용하지 않는 편이다. 코드 반복이나 재사용을 위한 가장 중요한 역할을 한다. 이하 일반적으로 널리 사용되는 함수라는 표현을 사용하겠다.

> 서브루틴은 코루틴과 함께 코드 흐름을 나타낸다.

함수를 구성함에 있어 미리 알아두어야 할 것이 있다.

1. 값을 전달하는 방법은 이전에 살펴본 것과 같이 스택을 사용한다. 
2. 함수 수신자와 함수 호출자 사이의 규칙과 함수가 사용하는 변수(식별자)에 대한 생존 범위(Life-time)를 관리하고 정의해야 한다.

함수 정의 구문은 아래와 같은 표현을 사용하기로 한다.

```scheme
(def main ()
     (+ 1 2))
```

이제 우리가 만드는 프로그래밍 언어는 `main` 함수를 사용하기로 약속하여 프로그램의 엔트리포인트entrypoint 라고 불리는 개념을 구현하기로 한다.

우리가 만든 S-expression 파서는 이 새로운 구문을 잘 파싱한다.

```js
const program = '(def main () (+ 1 2))';
```

`runner.js` 파일에 있는 프로그램 코드를 수정하여 파싱해 보자.

Phil 의 본문에 있는 node repl 에서 실행하는 방법도 유용하니 경험해 보도록 하자.

```shell
Welcome to Node.js v19.3.0.
Type ".help" for more information.
> const { parse } = require('./lib/parser');
undefined
> JSON.stringify(parse('(def main () (+ 1 2))'));
'[[["def","main",[0],["+",1,2]]],""]'
>
```

타입 정보가 다른게 있는지 "main" 뒤에 `[0]` 있는데 나중에 다시 점검해 보기로 한다.

파싱은 일단 되니 이 결과를 컴파일하는 코드를 보강해 보도록 한다.

우선 `def` 는 사용자가 정의하는 키워드가 아니고 이 언어 시스템의 고유한 키워드이다. 여기에서는 "primitive" 라고 부르고 있다.

이렇게 원시 함수를 모아두는 객체를 하나 만들어 둔다. 그리고 compile fn call 함수에서 원시primitive 함수를 따로 처리하는 기능을 추가할 것이다.

그 전에 인자가 없는 깡통 함수를 선언하고 있는 def main () 코드가 정상적으로 파싱 되도록 parser 를 수정해 보자.

문제는 여는 괄호가 블록을 시작하는 것도 있지만 인자를 선언하는 영역의 시작으로도 사용된다는 점이다. 우리의 기본 코드는 인자가 없는 상태로 제공되고 있고 이는 빈 expression 으로 반환되어야 한다.

Phil 의 저장소에 있는 코드를 참고하여 파서를 수정하자.

괄호를 닫는 과정에 저장된 토큰이 없는 경우에 대한 예외 처리를 추가해야 한다.

```ts
for (let index = 0; index < program.length; index++) {
// ...
    case ')':
      // check for parameter section in function declaration
      if (currentToken.length > 0) {
        tokens.push(getToken(currentToken));
      }
      return [tokens, program.substring(index + 1)];
    default:
// ...
}
```

아직 어셈블리로 컴파일은 되지 않지만 파서의 테스트 케이스는 통과하게 된다.

```ts
  it('should parse def main function expression', () => {
    const source = '(def main () (+ 1 2))';
    const expected = [[['def', 'main', [], ['+', 1, 2]]], ''];

    const result = parse(source);

    expect(result).toEqual(expected);
  });
```

다음 포스트에 코드를 생성하며 함수 정의definition와 호출/피호출을 나타낼 때 자주 사용되는 caller/callee 를 통해 함수 호출 규칙을 살펴 보고, 식별자의 유효 범위를 제한하는 scope 에 대해 알아보기로 하자.
