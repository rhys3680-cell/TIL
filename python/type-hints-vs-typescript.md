# Python 타입 힌트는 TypeScript와 다르다

## 핵심 차이

```
TypeScript: 타입 틀리면 → 컴파일 에러 (실행 자체가 안 됨)
Python:     타입 틀리면 → 아무 일도 안 일어남 (그냥 실행됨)
```

```python
# Python — 에러 없이 실행됨
x: int = "hello"    # int라고 선언했는데 문자열 넣어도 OK
print(x)            # "hello" 출력
```

Python 타입 힌트는 주석이다. 실행에 영향 없음.

## 그럼 왜 쓰나

에디터/도구가 경고해준다:

```python
def add(a: int, b: int) -> int:
    return a + b

add("hello", "world")  # VS Code에서 빨간 줄 (pyright/mypy)
                        # 하지만 실행하면 "helloworld" 나옴
```

별도 도구로 검사해야 함:

```bash
uv run mypy src/       # 타입 검사기
uv run pyright src/    # 또 다른 타입 검사기
```

## 비교

| | TypeScript | Python 타입 힌트 |
|---|---|---|
| 강제성 | 컴파일 시 강제 | 강제 아님 (무시 가능) |
| 실행 영향 | 타입 에러면 실행 불가 | 타입 틀려도 실행됨 |
| 검사 시점 | `tsc` 컴파일 시 자동 | `mypy`/`pyright` 별도 실행 |

## 설계 철학

Python은 "쓰고 싶은 사람만 써라"는 입장. 동적 타입이 Python이 느린 이유이기도 하다 — PVM이 매번 타입을 확인해야 하므로.

소규모 프로젝트에서는 타입 힌트보다 테스트로 검증하는 게 현실적.