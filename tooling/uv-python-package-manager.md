# uv — Rust로 만든 Python 패키지 매니저

## 특징

- Rust로 개발 → pip보다 10~100배 빠른 속도
- 가상환경, 의존성 관리, Python 버전 관리를 하나로 통합
- 병렬 처리, 전역 캐시, HTTP/2 지원으로 성능 최적화

## 주요 명령어

### 버전 확인

```bash
uv --version
```

### 프로젝트 초기화

```bash
uv init my_project
```

- `pyproject.toml`, `main.py`, `.venv/`, `.gitignore` 자동 생성
- 가상환경 자동 생성

### 실행

```bash
uv run main.py
```

실행 시 자동으로:
1. `.venv/` 없으면 가상환경 생성
2. `pyproject.toml` 기준으로 의존성 설치
3. `.venv/` 안의 Python으로 실행

`source .venv/bin/activate` 없이 바로 실행 가능.

### 의존성 추가

```bash
uv add selenium
uv add --dev pytest      # 개발 의존성
```

`pyproject.toml`에 자동 기록 + `.venv/`에 설치까지 한 번에.

### 의존성 동기화

```bash
uv sync
```

`pyproject.toml` 기준으로 `.venv/`를 맞춰줌. 다른 환경에서 clone 후 이걸 실행하면 동일한 환경 재현.

## pip와 비교

| | pip | uv |
|---|---|---|
| 속도 | 느림 | 10~100배 빠름 |
| 가상환경 | 별도 (`python -m venv`) | 자동 관리 |
| 의존성 잠금 | `requirements.txt` (수동) | `uv.lock` (자동) |
| Python 버전 관리 | 불가 (pyenv 별도 필요) | 내장 (`uv python install`) |
| 설정 파일 | `requirements.txt` | `pyproject.toml` |