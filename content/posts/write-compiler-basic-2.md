---
title: Write Deadly Simple Compiler Part 2
slug: write-simple-compiler-2
date: 2022-11-27T22:00:00+09:00 
author: soomtong 
tags: 
  - compiler 
  - assembliy
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

> [Phil](https://notes.eatonphil.com) 의 'Writing a lisp compiler from scratch in JavaScript' 첫 포스트의 두 번째 파트인데 분량이 많아 새 포스트를 열었다.

지난 포스트에 이어서... 

Phil 의 가이드는 S-expression 을 배열 데이터로 만들고 이를 어셈블리 코드(텍스트)로 기록하도록 하고 있다. 그후 어셈블러를 사용하여 실행 가능한 바이너리 파일을 만든다.

어셈블리는 보통의 사람이 읽을 수 있는 가장 낮은 수준의 프로그래밍 언어로 CPU 의 실제 명령과 1:1 로 대응된다. 그래서 생성된 바이너리 코드는 어셈블리로 쉽게 변환 가능하다.

어셈블리는 선형으로 구성된다. 비교와 반복을 통해 procedual 하게 코드가 실행된다. 함수가 호출될 때 특정 레지스터에 인자를 담아두고 함수가 종료되면 특정 레지스터에 반환 값이 담긴 후 다음 명령으로 되돌아가기 위해 약속된 레지스터를 참조한다. 

규칙만 잘 알면 단순하고 읽기 쉽다...🤯 어쨋든, 우리의 프로그램들은 이 과정이 매우 빠르게 반복되는 것 뿐이다.

> 각 CPU 제조사 마다 CPU 를 위한 명령어(OpCode) 세트가 있고 우리는 이걸 ISA, Instruction Set Architecture 라고 부른다.

이 포스트에서는 x86_64 기준, `RDI`, `RSI`, `RDX` 레지스터에 인자를 담고, `RAX` 레지서터에 반환 값을 담기로 한다. 함수 호출시 값을 담는 주체도 함수를 호출하는 쪽으로 하자.

어셈블리 프로그램은 section 으로 구분되어 있다. `text` 영역에는 읽기만 가능한 프로그램의 코드, 명령들이 있고 CPU 는 이 명령들을 실행하며 다음으로 이동한다. 이동 과정에 CALL, RET, JMP 등 다음 명령어의 위치를 변경하는 명령을 만나 이동하기도 하고 복귀하기도 한다.

그리고 중요한 부분으로 보통, 프로그램의 시작 위치를 정의하는 부분인 `.global main` 도 필요하다. 
main 은 goto 를 위한 흔한 라벨이다. main 이라는 라벨이 사용될 수 있으니 우리 프로그램의 시작은 `_main_` 을 사용하도록 하겠다.

각 어셈블리 코드를 생성하는 emit 이라는 함수를 중심으로 우리가 가진 AST 데이터를 어셈블리 코드로 변환해 보자.

## 코드 생성

emit 함수가 생성하는 결과는 소스 코드가 된다. 들여쓰기를 위해 depth 와 기록할 code 를 인자로 받자.

```ts
const makeIndent = (length: number) => {
  let indentSpace = '';

  for (let i = length; i > 0; i -= 1) {
    indentSpace += '\t';
  }

  return indentSpace;
};

function emit(indentDepth: number, code: string, print: boolean = true): string {
  const dump = `${makeIndent(indentDepth)}${code}`;

  if (print) {
    console.log(dump);
  }

  return `${dump}\n`;
}
```

정상 동작을 확인하기 위한 단순한 테스트 파일도 추가해 본다.

```ts
import { emit } from './compiler';

describe('Emit tab spaces', () => {
  it('should return tabbed spaces with text', () => {
    const expected = '\t\thello'

    const result = emit(2, 'hello');

    expect(result).toEqual(expected);
  });
});
```

이어서 함수 인자argument를 처리하는 코드와 함수를 호출하고 결과를 저장하는 코드를 생성하는 compile arguement, compile function(label) call 함수를 구성해 보자.

우리가 가진 AST 구조를 생각해 보자.

```js
['+', 1, ['+', 2, 3]]
```

연산자는 어셈블리로 구성된 함수가 되고 숫자는 레지스터에 할당될 값들이 된다. 함수는 함수 라벨로 표기하여 CPU 명령 처리기가 Jump 할 수 있도록 하고 각 숫자는 리터럴이 된다.

어셈블리 코드는 아래와 같은 형식으로 시작하고 시스템 콜을 통해 종료된다. (예시는 요즘 macOS 기준)

```asm
  .global _main_

  .text
  
  // 프로그램 코드...
```
```asm
  // 프로그램 코드...

  MOV RDI, RAX
  MOV RAX, 0x2000001
  SYSCALL
```

우리의 샘플 AST 는 두 번의 `ADD` 연산을 하게 될 예정이고 이 함수는 `plus:` 라는 라벨을 달고 재사용할 것이다.

어셈블리 수준에서 함수 호출 기능은 아래와 같은 형식으로 구성될 수 있다.

1. 연산에 사용될 인자를 레지스터에 저장하기 위해 레지스터의 이전 값을 저장 → 각 레지스터를 스택에 저장 → 호출하는 함수 인자에 갯수 만큼만 스택에 올리면 된다.
2. 우리 수준에서, 최대 3개까지 사용 가능한 인자들을 레지스터에 저장 (순서대로)
3. 함수 호출 → 더하기 기능을 하는 프로시져로 이동 → 코드 실행 후 RET 명령으로 복귀
4. 스택에 저장된 레지스터 값을 복원 (저장한 역순으로)
5. 결과 값이 저장된 레지스터에서 값을 확인

Phil 의 코드를 참고로 좀 더 진행해 보자.

파라미터를 위한 3개의 레지스터를 준비하고, 어셈블리 내장 함수를 감싼 plus 함수 라벨도 준비해 두었다.

```ts
enum OpCode {
  '+' = '+',
}

enum Register {
  RDI = 'RDI',
  RSI = 'RSI',
  RDX = 'RDX',
  RAX = 'RAX',
}

const ArgRegisters: Register[] = [Register.RDI, Register.RSI, Register.RDX];

const BuiltinFunctions: Record<OpCode, string> = {
  '+': 'plus',
};
```

함수 호출 생성하기 위한 compileFnCall 함수를 작성해 보자. 위에서 안내한 pseudo 코드를 옮기면 된다.

```ts
function compileFnCall(functionLabel: OpCode, args: Literal[], destination?: string): string {
  let dump = '';

  dump += args.reduce((acc, _, index) => {
    return acc + emit(1, `PUSH ${ArgRegisters[index]}`);
  }, '');

  dump += args.reduce((acc, arg, index) => {
    return acc + compileArgument(arg, ArgRegisters[index]);
  }, '');

  dump += emit(1, `CALL ${BuiltinFunctions[functionLabel] || functionLabel}`);

  dump += args.reduceRight((acc, _, index) => {
    return acc + emit(1, `POP ${ArgRegisters[index]}`);
  }, '');

  if (destination) {
    dump += emit(1, `MOV ${destination}, ${Register.RDX}`);
  }

  return dump;
}
```

스택에 올린 기존 레지스터 값들은 역순으로 복원하기 위해 reduceRight 를 사용했다.

다시 한 번 우리의 AST 를 보자.

```js
['+', 1, ['+', 2, 3]]
```

인자 보관용으로 사용할 레지스터를 스택에 올려 두었다면 AST 의 첫 번째 인자를 OpCode 로 할당하고 이어지는 인자를 OpCode 의 인자가 될 리터럴로 간주하여 레지스터에 올려두어야 한다.

이 과정에서 이어지는 인자가 배열인 경우는 또 다른 함수 호출이 예상되기 때문에 또 다시 OpCode 가 호출되는 재귀 함수 구성을 가지게 된다.

그래서 인자를 compile 하는 함수는 인자가 배열일 경우를 구분하여 어샘블리 코드를 emit 하도록 하자.

```ts
function compileArgument(arg: Literal | Literal[], destination: string): string {
  if (Array.isArray(arg)) {
    const [opcode, ...rest] = arg;
    return compileFnCall(opcode as OpCode, rest, destination);
  }

  return emit(1, `MOV ${destination}, ${arg}`);
}
```

배열이 아닌 일반 리터럴이라면 대상 레지스터에 값을 지정하는 것으로 충분하다.

이제 테스트 코드를 추가하고 AST 를 받아 어셈블리로 컴파일하는 코드를 작성하자.

```ts
describe('Emit compiled code for arguments with destication', () => {
  it('should return single move statement', () => {
    const expected = '\tMOV RDI, 1\n';
    const result = compileArgument(1, 'RDI');

    expect(result).toEqual(expected);
  });

  it('should call compileCall function when got array of arguments', () => {
    const expected = `\tPUSH RDI
      // ...`;
    const result = compileArgument(['+', 1, 2], 'RSI');

    expect(result).toEqual(expected);
  });
});

describe('Emit compiled code for function call with opcode and arguments', () => {
  it('should return basic assembly code', () => {
    const expected = `\tPUSH RDI 
      // ...`;
    const result = compileFnCall('+' as OpCode, [1, 2]);

    expect(result).toEqual(expected);
  });
});
```

이제 어셈블리 본문을 완성해 보자. 먼저 prefix, postfix 용 함수를 추가하자. prefix 쪽에 plus 라벨을 가진 ADD 함수를 작성해 두었다.

```ts
function emitPrefix() {
  emit(1, '.global _main_');
  emit(1, '.text');

  emit(0, 'plus:');
  emit(1, 'ADD RDI, RSI');
  emit(1, 'MOV RAX, RDI');
  emit(1, 'RET');

  emit(0, '_main_:');
}

function emitPostfix() {
  emit(1, 'MOV RDI, RAX');
  // Set exit arg in macOS
  emit(1, 'MOV RAX, 0x2000001');
  // Set syscall number
  emit(1, 'SYSCALL');
}
```

이 모든 작업을 완성시킬 compile 함수는 아래와 같다.

```ts
function compile(ast: Literal[]) {
  const [opcode, ...rest] = ast;

  emitPrefix();
  compileFnCall(opcode as OpCode, rest);
  emitPostfix();
}
```

> 각 emit 함수가 반환하는 값을 모아서 처리하는 구문은 생략되었으니 저장소의 소스코드를 참고;
> https://github.com/soomtong/simple-lisp-compiler

사용된 테스트 코드를 살펴 보자.

```ts
describe('Emit basic AST into assembly code', () => {
  it('should emit a assembly code!', () => {
    const expected = `// skipped whitespace
  .global _main_
  .text
plus:
  ADD RDI, RSI
  MOV RAX, RDI
  RET
_main_:
  PUSH RDI
  // ...
  MOV RAX, 0x2000001
  SYSCALL
`;
    const result = compile(['+', 1, ['+', 2, 3]]);

    expect(result).toEqual(expected);
  });
});
```

자동으로 스택이 관리되는 것이 이해되면 다음 파트로 이동해 보자.
