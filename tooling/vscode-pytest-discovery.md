# VS Code pytest discovery가 SIGTERM으로 실패할 때

> 2026-02-26

## 상황

VS Code 테스트 탭에서 테스트가 발견되지 않고, 로그에 아래 에러가 출력되었다.

```
pytest discovery failed with exit code null and signal SIGTERM
```

## 원인

`.vscode/settings.json`의 `pytestArgs`에 `--log-cli-level=INFO`를 넣으면, discovery 단계에서 모든 테스트의 로그를 출력하려다가 타임아웃이 발생할 수 있다.

## 해결

`--log-cli-level=INFO`를 `pytestArgs`에서 빼고, 필요할 때 터미널에서 직접 사용한다.

```json
{
    "python.testing.pytestArgs": [
        "tests",
        "-s",
        "-v",
        "--tb=short"
    ]
}
```

logging 출력이 필요할 때:

```bash
uv run pytest -sv --log-cli-level=INFO
```

## 핵심

- VS Code의 `pytestArgs`는 discovery에도 적용됨
- 무거운 옵션은 `pytestArgs`에 넣지 말고 터미널에서 직접 사용
- `-s`, `-v`, `--tb=short` 정도가 적당