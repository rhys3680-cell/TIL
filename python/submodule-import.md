# Python 서브모듈은 명시적으로 import해야 한다

> 2026-02-26

## 상황

`src/` 패키지 안에 `config.py` 모듈이 있는 구조에서, 테스트 코드에서 `import src`만 했더니 `src.config`에 접근할 수 없었다.

```python
import src
src.config.TARGET_URL  # ❌ AttributeError
```

## 원인

Python에서 패키지를 import하면 `__init__.py`만 실행된다. 하위 모듈은 자동으로 로드되지 않는다.

```
src/
├── __init__.py   ← import src 시 이것만 실행됨
└── config.py     ← 자동으로 안 읽힘
```

## 해결

서브모듈까지 명시적으로 import한다.

```python
import src.config        # __init__.py + config.py 둘 다 로드
src.config.TARGET_URL    # ✅ 접근 가능
```

또는 `from` 구문으로 직접 가져온다.

```python
from src.config import TARGET_URL
```

## `__init__.py`에서 re-export하는 방법도 있다

```python
# src/__init__.py
from src import config
```

이러면 `import src`만으로 `src.config` 접근이 가능하다. 하지만 내부 애플리케이션에서는 구조가 안정되기 전까지 `__init__.py`를 비워두고 명시적 import를 쓰는 게 유지보수에 유리하다.

## 핵심

- `import 패키지` ≠ 하위 모듈 전체 로드
- 서브모듈은 항상 명시적으로 import
- `__init__.py` re-export는 라이브러리/SDK처럼 외부에 API를 제공할 때 적합