# AST, 바이트코드, PVM — Python 실행의 3단계

## AST (추상 구문 트리)

코드를 의미 단위로 분해한 트리 구조.

```python
x = 1 + 2
```

```
     Assign (할당)
    /        \
Name("x")   BinOp (이항 연산)
            /   |   \
         1      +     2
```

직접 확인 가능:

```python
import ast
tree = ast.parse("x = 1 + 2")
print(ast.dump(tree, indent=2))
```

## 바이트코드

AST를 PVM이 이해하는 명령어 목록으로 변환한 것. 어셈블리어와 비슷해 보이지만 다르다.

```python
import dis
dis.dis("x = 1 + 2")
#  LOAD_CONST    3      ← 상수 3을 스택에 (1+2 미리 계산)
#  STORE_NAME    x      ← 스택 맨 위를 x에 저장
```

### 어셈블리어 vs 바이트코드

| | 어셈블리어 | 바이트코드 |
|---|---|---|
| 실행 주체 | CPU (하드웨어) | PVM (소프트웨어) |
| 대상 | 특정 CPU (x86, ARM) | Python VM (OS 무관) |
| 속도 | 매우 빠름 | 느림 |
| 예시 | `MOV`, `ADD`, `JMP` | `LOAD_CONST`, `STORE_NAME` |

### 스택 기반 실행

바이트코드는 스택 기반으로 동작한다:

```python
dis.dis("y = a + b * c")
#  LOAD_NAME     a
#  LOAD_NAME     b
#  LOAD_NAME     c
#  BINARY_OP     *      ← b * c
#  BINARY_OP     +      ← a + (b*c)
#  STORE_NAME    y
```

```
          [c]
   [b]    [b]    [b*c]
   [a] →  [a] →  [a]   →  [a+b*c]  →  y = a+b*c
```

## PVM (Python Virtual Machine)

바이트코드를 한 줄씩 읽고 실행하는 프로그램. 본질적으로 C로 짠 while 루프다 (CPython 기준):

```c
// 극도로 단순화한 PVM 핵심
while (1) {
    명령어 = 다음_바이트코드();
    switch (명령어) {
        case LOAD_CONST:  스택에_올리기(값);  break;
        case STORE_NAME:  변수에_저장(이름, 스택에서_꺼내기());  break;
        case BINARY_OP:   a = 꺼내기(); b = 꺼내기(); 올리기(a+b);  break;
    }
}
```

## .pyc의 "c"는 C 언어가 아니라 "compiled"

```
.py  = Python source (소스코드)
.pyc = Python compiled (컴파일된 바이트코드)
```

## 전체 레벨 비교

```
Python 코드:     x = 1 + 2           ← 사람이 읽는 언어
                    ↓ 컴파일
바이트코드:      LOAD_CONST 3         ← PVM이 읽는 언어 (.pyc)
                 STORE_NAME x
                    ↓ PVM이 해석
C 코드 (PVM):   stack_push(3);       ← PVM 내부 구현
                    ↓ C 컴파일러
어셈블리/기계어:  MOV EAX, 3          ← CPU가 읽는 언어
                    ↓
CPU 실행
```

Python이 느린 본질적 이유: 모든 바이트코드 명령어마다 PVM의 switch문을 거쳐야 하는 해석 오버헤드가 있다. C는 CPU가 기계어를 직접 실행하지만, Python은 PVM이라는 중간 해석 단계가 있는 이중 구조.