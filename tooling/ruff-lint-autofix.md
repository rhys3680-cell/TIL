# ruff 린트 실행과 자동 수정

## 배경

프로젝트에 ruff가 dev 의존성으로 설치되어 있으면 uv로 바로 실행할 수 있다.

## 사용법

```bash
uv run ruff check .          # 린트 검사
uv run ruff check . --fix    # 자동 수정 가능한 것 수정
```

## 자주 만나는 규칙

| 코드 | 설명 | 자동 수정 |
|---|---|---|
| I001 | import 정렬 안 됨 | O |
| E501 | 줄 길이 초과 (88자) | X |
| E, F | pycodestyle, pyflakes | 일부 |

## E501 줄 길이 해결법 — 괄호로 문자열 분리

Python에서 괄호 안의 문자열 리터럴은 자동으로 합쳐진다:

```python
# Before (126자 — E501 위반)
USER_AGENT = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36"

# After
USER_AGENT = (
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) "
    "AppleWebKit/537.36 (KHTML, like Gecko) "
    "Chrome/131.0.0.0 Safari/537.36"
)
```

## .ruff_cache

`ruff check`를 실행하면 `.ruff_cache/` 디렉토리가 생성된다. 변경되지 않은 파일을 재검사하지 않기 위한 캐시로, `.gitignore`에 추가해두면 된다.