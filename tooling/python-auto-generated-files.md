# Python 프로젝트에서 자동 생성되는 파일들

## 목록

| 디렉토리/파일 | 누가 만드나 | 역할 |
|---|---|---|
| `__pycache__/` | Python | 바이트코드 캐시 (.pyc) |
| `.pytest_cache/` | pytest | 마지막 실패 테스트 기억, `--lf` 옵션에 사용 |
| `*.egg-info/` | setuptools (빌드) | 패키지 메타데이터. hatchling은 대신 `.dist-info`를 `.venv/` 안에 생성 |
| `.ruff_cache/` | ruff | 린트 결과 캐시, 변경 안 된 파일 재검사 방지 |
| `.venv/` | uv | 가상환경 (의존성 설치 위치) |

## 공통 특징

- 전부 로컬 캐시이므로 git에 올릴 필요 없음
- 지워도 자동으로 다시 생성됨
- `.gitignore`에 추가해둬야 함

## .gitignore 설정

```gitignore
# Python-generated files
__pycache__/
*.py[oc]
build/
dist/
wheels/
*.egg-info

# Cache
.pytest_cache/
.ruff_cache/

# Virtual environments
.venv
```

## 참고

- `.gitignore`에 추가해도 VS Code 파일 탐색기에서는 보인다. 숨기려면 `.vscode/settings.json`에 `files.exclude` 설정 필요.
- git이 이미 추적 중인 파일은 `.gitignore`에 추가해도 바로 적용 안 됨. `git rm --cached`로 추적 해제 필요.