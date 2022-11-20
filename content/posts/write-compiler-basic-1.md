---
title: Write Deadly Simple Compiler 
slug: write-simple-compiler-1 
date: 2022-11-19T22:00:00+09:00 
author: soomtong 
tags: 
  - compiler 
  - lisp 
  - javascript 
keywords: 
  - compiler
  - parser
description: "내가 처음으로 구독한 개인 메일인 Phil 의 웹 페이지에 있는 2018년 말 포스트인 'Writing a lisp compiler from scratch in JavaScript' 를 학습하며 기록한 글이다."
showFullContent: false
readingTime: true
---

> 내가 처음으로 구독한 개인 메일인 [Phil](https://notes.eatonphil.com) 의 웹 페이지에 있는 2018년 말 포스트인 'Writing a lisp compiler from scratch in JavaScript' 를 학습하며 기록한 글이다.

우선 Node 환경에서 구동되는 Lisp 컴파일러를 구현하는 투토리얼을 따라가 보도록 한다. 본문은 여기에 있다.

- https://notes.eatonphil.com/compiler-basics-lisp-to-assembly.html

시리즈의 목표는 아래와 같은 코드를 계산하는 프로그램을 만드는 것이다.

```scheme
(+ 1 (+ (2 3)))
```

이 과정에서 '파싱', '코드 생성', '어셈블리 기본', '시스템 콜' 에 대한 내용을 다루지만 '함수 정의', '비 심볼/비 산술 데이터 타입', '3개 이상의 인자 전달', '예외 처리', '에러 핸들링' 등은 다루지 않는다.

## 파싱

이 강의에서 lisp 에서 사용되는 S-expression 문법을 처리하는 컴파일러를 만들 것이다. S-expression 은 파싱하기 쉽기 때문에 선택되었다. 그리고, 우리의 프로그램은 기능이 제한적이기 때문에 렉싱lexing과 파싱parsing 단계를 나누지 않고 진행한다.

렉서와 파서를 통해 컴파일러를 만드는 것은 다음 기회에 살펴보자. 또는 파서와 렉서를 만들어 보는 시간을 가지는 것도 좋겠다.

> 언어 문법을 디자인하고 파싱하고 렉싱하는 유명한 소프트웨어들이 있다. 해당 언어를 위한 파서를 만들어주는 프로그램도 있다. lex, yacc, bison, menhir, peg, antlr, lalrpop 등 본격적으로 언어를 만든다면 이런 툴을 사용하지 않을까?
> https://en.wikipedia.org/wiki/Comparison_of_parser_generators

파서는 `구문 추상 트리abstract syntax tree` 를 만들어 낸다. 트리 구조는 중첩된 배열로 표현된다.

```js
(+ 1 (+ 2 3)) → ['+', 1, ['+', 2, 3]]
```

처음 이 과제를 자바스크립트가 아닌 다른/새로운 언어를 공부하면서 연습해 보면서 구현하려 했다. 하지만, 디자인도 안나왔는데 테스트 코드를 작성하는 것처럼 어색한게 진도가 나가지 않았다. 익숙한 언어로 먼저 진행해 보자.

1. TypeScript
2. Grain
3. Zig

정도 경험해 보면 어떨까 싶다.

먼저 parse 함수를 만들자. parse 함수는 아래의 형태를 가질 것이다. 타입 정보는 과정이 진행되면서 개선해 간다.

```ts
export const parse = (program: unknown): unknown[] => {
  const tokens: unknown[] = [];
  // todo: 
  return [tokens, ''];
}
```

소스 코드를 program 으로 받아 어떤 처리를 하여 AST(abstract syntax tree)를 얻어낸다. parse 함수는 처리가 성공하면 반환되는 결과의 두 번째 요소에 빈 문자열을 전달하는 것으로 규칙을 삼았다.

> 그래도 왜 반환이 [tokens, ''] 인지 아직 모르겠다. 그냥 타입을 이렇게 작성한 거라면 나중에 바꾸도록 하자.

공백이나 괄호를 만날 때까지 문자열을 더해 토큰을 만들어 추가하는 코드로 개선해 보자.

```ts
export const parse = (program: string): unknown[] => {
  const tokens: (string | number)[] = [];
  let currentToken: string = '';

  for (const char of program) {
    switch (char) {
      case '(':
        // todo:
        break;
      case ')':
        // todo:
        break;
      case ' ':
        tokens.push(Number.isFinite(currentToken) ? Number(currentToken) : currentToken);
        currentToken = '';
        break;
      default:
        currentToken += char;
        break;
    }
  }

  return [tokens, ''];
};
```

계산을 위해 숫자인 경우는 연산 가능한 타입으로 변경해 주고 있다.

그 다음은 괄호를 만나게 되는 경우를 처리하자. 

괄호를 열면 또 다시 파싱할 문자열이 들어있다. 즉, 재귀 가능한 구조라는 것이다. 여는 괄호 코드를 구현하기 전에 닫는 괄호를 먼저 구성하자. 

단어를 읽다가 닫는 괄호를 만나면 여기까지 토큰을 저장하고 재귀 호출한 parse 함수에게 현재까지 모아둔 토큰과 이어서 처리해야할 남은 문자열을 반환한다.

남은 문자열 반환을 위해 substring 이 필요해 for of 대신 forEach 를 사용하기로 했다.

```ts
export const parse = (program: string): unknown[] => {
  const tokens: (string | number)[] = [];
  let currentToken: string = '';

  program.forEach((char: string, index: number) => {
    switch (char) {
      case '(':
        // todo:
        break;
      case ')':
        return [tokens, program.substring(index + 1)];
      case ' ':
        tokens.push(Number.isFinite(currentToken) ? Number(currentToken) : currentToken);
        currentToken = '';
        break;
      default:
        currentToken += char;
        break;
    }
  });

  return [tokens, ''];
};
```

이제 여는 괄호의 코드를 구성하자. 재귀 호출이 시작되는 구간이다.

```ts
export const parse = (program: string): any[] => {
  const tokens: (string | number)[] = [];
  let currentToken: string = '';

  Array.from(program).forEach((char: string, index: number) => {
    switch (char) {
      case '(':
        const [parsedToken, rest] = parse(program.substring(index + 1));
        tokens.push(parsedToken);
        break;
      case ')':
        return [tokens, program.substring(index + 1)];
      case ' ':
        tokens.push(Number.isFinite(currentToken) ? Number(currentToken) : currentToken);
        currentToken = '';
        break;
      default:
        currentToken += char;
        break;
    }
  });

  return [tokens, ''];
};
```
정상적으로 종료되는 경우 반환은 파싱된 토큰 뭉치와 빈 문자열인게 납득이 된다. 더 이상 파싱할 문자열이 없을 때에 해당되는 것이다.

대강 이 쯤 되니 spec 파일을 만들 수 있을 것 같다. 테스트 케이스를 만들고 타입 정보를 제대로 구성해 보자.

```ts
import { parse } from './parser';

describe('Parse s-expression', () => {
  it('should parse basic expression', () => {
    const source = '(+ 3 (+ 1 2)';
    const expected = [[['+', 3, ['+', 1, 2]]], ''];

    const result = parse(source);

    expect(result).toEqual(expected);
  });
});
```

따로 분리한 타입 정보는 우선 이 정도로 구성되었다.

```ts
export type Program = string;

export type Token = string | number | boolean | Token[];

export type ParsedTokens = [Token, Program];
```

제대로 동작하지 않는 첫 파서는 아래와 같다.

```ts
import type { Program, Token, ParsedTokens } from './parser.type';

export const parse = (program: Program): ParsedTokens => {
  const tokens: Token[] = [];
  let currentToken: Token = '';

  // eslint-disable-next-line @typescript-eslint/ban-ts-comment
  // @ts-ignore
  Array.from(program).forEach((char: string, index: number) => {
    switch (char) {
      case '(': {
        const [parsedToken, rest] = parse(program.substring(index + 1));
        tokens.push(parsedToken);
        // reset rest and index?
        break;
      }
      case ')':
        tokens.push(Number.isNaN(Number(currentToken)) ? currentToken : Number(currentToken));
        return [tokens, program.substring(index + 1)];
      case ' ':
        tokens.push(Number.isNaN(Number(currentToken)) ? currentToken : Number(currentToken));
        currentToken = '';
        break;
      default:
        currentToken += char;
        break;
    }
  });

  return [tokens, ''];
};
```

재귀 함수를 통해 처리된 다음 프로그램이 또 다시 파싱되어 배열이 필요 이상 중첩되는 결과를 만나게 된다.

```
console.dir
[
  [
    [ '+', 3, [ '+', 1, 2 ], '+', 1, 2 ],
    '+',
    3,
    [ '+', 1, 2 ],
    '+',
    1,
    2
  ],
  ''
]
```

재귀 함수를 통해 반환 된 나머지 프로그램 코드에 대해 다시 tokenize 를 할 필요가 없기 때문에 재귀 함수를 통해 처리된 토크을 제외한 나머지 프로그램만 이전 parse 단계를 가지도록 프로그램 문자열을 추려 낼 필요가 있게 되어 forEach 를 전통적인 인덱스를 가지는 for 문으로 변경해야 한다.

> 파서는 문자열 커서를 앞으로 뒤로 이동하며 토큰을 생성해 내는데 재귀 함수를 통해 처리한 내용은 건너 뛰어야 하는 기능이 필요한 것이다.

```ts
const getToken = (source: Program): Token => (Number.isNaN(Number(source)) ? source : Number(source));
```
```ts
for (let index = 0; index < program.length; index++) {
  const char = program.charAt(index);

  switch (char) {
    case '(': {
      const [parsedToken, restProgram] = parse(program.substring(index + 1));
      tokens.push(parsedToken);
      // reset program with skipped by recursive and index to 0
      program = restProgram;
      index = 0;
      break;
    }
    case ')':
      tokens.push(getToken(currentToken));
      return [tokens, program.substring(index + 1)];
    case ' ':
      tokens.push(getToken(currentToken));
      currentToken = '';
      break;
    default:
      currentToken += char;
      break;
  }
}
```

파싱 함수 반복문을 Phil 의 코드에 맞게 수정하고 `npm t` 를 통해 테스트를 성공시켰다.

```
> simple-compiler@0.1.0 test
> jest

 PASS  src/parser.spec.ts
  Parse s-expression
    ✓ should parse basic expression (2 ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        1.161 s, estimated 2 s
Ran all test suites.
```

이어 파싱된 데이터를 어셈블리로 변환하는 단계로 넘어가자.

어셈블리를 바이너리로 변환하는 것은 gcc 나 llvm 의 도움을 받는다.
