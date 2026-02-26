# pytest에서 .env 환경변수를 테스트하려면 load_dotenv를 mock해야 한다

> 2026-02-26

## 상황

`.env` 파일로 환경변수를 관리하는 `config.py`를 테스트할 때, 환경변수가 없는 경우를 테스트하고 싶었다. `monkeypatch.delenv`로 환경변수를 지우고 모듈을 reload했지만, 값이 그대로 남아있었다.

```python
def test_missing_target_url(monkeypatch):
    monkeypatch.delenv("TARGET_URL", raising=False)
    reload(src.config)
    assert src.config.TARGET_URL == ""  # ❌ 여전히 URL 값이 있음
```

## 원인

`config.py`의 `load_dotenv()`가 `.env` 파일을 읽어서 OS 환경변수에 다시 세팅한다. reload하면 `load_dotenv()`가 다시 실행되면서 지운 값을 복원해버린다.

```
환경변수 삭제 → reload → load_dotenv() 실행 → .env에서 다시 읽어옴 → 원래 값 복원
```

## 해결

`unittest.mock.patch`로 `load_dotenv`를 무력화한다.

```python
from unittest.mock import patch

def test_missing_target_url():
    original = os.environ.pop("TARGET_URL", None)
    try:
        with patch("dotenv.load_dotenv"):  # .env 로딩 차단
            reload(src.config)
        assert src.config.TARGET_URL == ""  # ✅
    finally:
        if original is not None:
            os.environ["TARGET_URL"] = original
        reload(src.config)
```

## 주의: patch 대상 경로

```python
patch("src.config.load_dotenv")   # ❌ 동작 안 함
patch("dotenv.load_dotenv")       # ✅ 원본 모듈을 mock해야 함
```

`src.config`에서 `from dotenv import load_dotenv`로 이미 import한 참조를 mock하는 게 아니라, `dotenv` 모듈의 원본 함수를 mock해야 `reload` 시 적용된다.

## 핵심

- `.env` + `load_dotenv` 환경에서 환경변수 부재 테스트 시 mock 필수
- `os.environ.pop`으로 직접 제거 + `patch`로 재로딩 차단을 조합
- `finally`에서 원래 값을 복원해야 다른 테스트에 영향 안 줌