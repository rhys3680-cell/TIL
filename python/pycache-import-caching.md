# Python import는 파일 실행이다 — __pycache__가 필요한 이유

## 배경

Python에는 JavaScript 번들러 같은 빌드 단계가 없다. `import`를 만나면 그 자리에서 `.py` 파일을 찾아서 실행한다.

## 핵심

| | JavaScript (번들러) | Python |
|---|---|---|
| 시점 | 빌드 시 전체 분석 | 런타임에 하나씩 |
| import 의미 | 의존성 선언 | 파일 실행 |
| 서브모듈 | 번들러가 알아서 포함 | 명시적으로 import해야 함 |

`import src`는 `src/__init__.py`만 실행한다. 서브모듈을 자동으로 안 불러오는 이유는 불필요한 코드 실행(부작용)을 방지하기 위해서.

## __pycache__ 캐싱

런타임 import라서 느릴 수 있다. Python은 이를 두 단계로 보완한다:

1. **바이트코드 캐시**: 첫 import 시 `.pyc` 파일을 `__pycache__/`에 저장. 다음부터는 컴파일 생략.
2. **sys.modules 캐시**: 같은 프로세스 안에서 동일 모듈을 다시 import하면 실행 없이 캐시에서 반환.

```python
import src.config   # ← 파일 실행됨
import src.config   # ← sys.modules에서 바로 반환 (실행 안 함)
```

## 결론

Python은 빌드 없이 바로 실행하는 인터프리터 언어라서 import가 런타임에 일어난다. 그 대신 `__pycache__`와 `sys.modules` 캐싱으로 반복 실행 비용을 줄인다. 크롤링처럼 네트워크 I/O가 병목인 경우 import 속도는 무의미하다.