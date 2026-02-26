# 가상환경(venv)은 가상머신(VM)이 아니다

## 배경

"virtual environment"라는 이름 때문에 가상머신과 혼동할 수 있다.

## 실제 격리 수준

```
VM:         [호스트 OS] → [가상 하드웨어] → [게스트 OS] → [앱]
Docker:     [호스트 OS] → [컨테이너 격리(커널 공유)] → [앱]
venv:       [같은 OS, 같은 커널] → [폴더 경로만 다름]
```

## venv가 하는 일

Python 실행 경로와 패키지 설치 위치를 바꿔주는 게 전부다.

```bash
# 가상환경 없이
python → C:\Python312\python.exe
pip install → C:\Python312\Lib\site-packages\ (전역)

# 가상환경 활성화 후
python → C:\project\.venv\Scripts\python.exe
pip install → C:\project\.venv\Lib\site-packages\ (프로젝트 전용)
```

OS, 파일 시스템, 네트워크 전부 동일하다. 격리라기보다 **패키지 설치 경로 분리**에 가까움.

## uv를 쓰면 직접 관리할 필요 없음

```bash
uv sync    # .venv/ 생성 + 의존성 설치 (자동)
uv run     # .venv/ 안의 Python으로 실행 (자동)
uv add     # .venv/에 패키지 설치 (자동)
```

uv가 가상환경의 생성, 활성화, 동기화를 전부 자동화해준다.

## 결론

venv의 "virtual"은 하드웨어나 OS 가상화가 아니라 PATH 환경변수 수준의 경로 분리를 의미한다.