# Python 실행 전체 과정 — .py에서 결과까지

## 전체 흐름

```
소스코드 (.py)
    │
    ▼
파싱 (Parsing) — 텍스트를 AST(구문 트리)로 변환
    │
    ▼
컴파일 (Compile) — AST를 바이트코드로 변환
    │
    ├──→ __pycache__/*.pyc에 저장 (다음 실행 시 파싱+컴파일 생략)
    │
    ▼
실행 (Execute) — PVM이 바이트코드를 한 줄씩 해석·실행
    │
    ├──→ import를 만나면 해당 모듈에 대해 같은 과정 반복
    │    (sys.modules에 있으면 생략)
    │
    ▼
결과
```

## 각 단계

### 1. 파싱: 텍스트 → AST

```python
x = 1 + 2

# AST (추상 구문 트리)
# Assign
#   target: Name("x")
#   value: BinOp(1, +, 2)
```

### 2. 컴파일: AST → 바이트코드

```python
import dis
dis.dis("x = 1 + 2")
#  LOAD_CONST  3 (3)      ← 1+2를 미리 계산
#  STORE_NAME  0 (x)      ← x에 저장
```

바이트코드는 CPU가 실행하는 기계어가 아니라 PVM이 이해하는 명령어.

### 3. PVM (Python Virtual Machine)

```
Java:    .java → 컴파일 → .class → JVM이 실행
Python:  .py  → 컴파일 → .pyc  → PVM이 실행
C:       .c   → 컴파일 → .exe  → CPU가 직접 실행
```

PVM은 바이트코드를 한 줄씩 읽어서 실행하는 소프트웨어 인터프리터. CPU 직접 실행보다 느리지만 OS에 독립적.

## import 발생 시 흐름

```
import src.config 만남
│
├─ sys.modules에 있나? → 있으면 바로 반환 (끝)
│
├─ __pycache__/config.cpython-312.pyc 있나?
│   ├─ 있고 .py보다 새로움 → .pyc 로드 (파싱+컴파일 생략)
│   └─ 없거나 오래됨 → 파싱 → 컴파일 → .pyc 저장
│
├─ 바이트코드 실행 (모듈 레벨 코드 실행)
│
└─ 결과(모듈 객체)를 sys.modules에 저장
```

## 캐시 요약

| 단계 | 입력 → 출력 | 캐시 | 수명 |
|---|---|---|---|
| 파싱+컴파일 | .py → 바이트코드 | `__pycache__/*.pyc` | 디스크 (영구) |
| 실행 | 바이트코드 → 모듈 객체 | `sys.modules` | 메모리 (프로세스 종료 시 소멸) |

## Python이 느린 이유

```
C/Rust:     소스 → 기계어 → CPU 직접 실행
JavaScript: 소스 → V8 JIT → 기계어 변환 → CPU 실행 (런타임 최적화)
Python:     소스 → 바이트코드 → PVM이 해석하며 실행 (매번 해석)
```

CPython은 JIT 컴파일이 없어서 바이트코드를 매번 해석한다. V8은 자주 쓰는 코드를 기계어로 변환하지만, Python은 그렇지 않다.

> 참고: Python 3.13부터 실험적 JIT이 도입되고 있음.