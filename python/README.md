# Python

## 1. Python 가상환경(venv) 설정 및 활성화

Python 3.3 버전 이후부터는 `venv` 모듈이 내장되어 있어 별도의 설치 없이 바로 사용할 수 있습니다.

### 단계 1: 가상환경 생성
터미널(또는 명령 프롬프트)에서 프로젝트 폴더로 이동한 후 아래 명령어를 입력하세요.
```bash
# 'venv'라는 이름의 가상환경 폴더를 생성합니다.
python -m venv venv
```

### 단계 2: 가상환경 활성화
운영체제에 따라 활성화 명령어가 다릅니다.

| 운영체제 | 명령어 |
| :--- | :--- |
| **Windows (CMD)** | `call venv\Scripts\activate` |
| **Windows (PowerShell)** | `.\venv\Scripts\Activate.ps1` |
| **macOS / Linux** | `source venv/bin/activate` |

> **Tip:** 활성화가 성공하면 터미널 프롬프트 앞에 `(venv)`라는 표시가 나타납니다.

---

## 2. pip 업데이트 및 패키지 관리

가상환경을 활성화한 상태에서 `pip`를 최신 버전으로 업데이트하는 것이 좋습니다.

### pip 최신 버전 업데이트
```bash
python -m pip install --upgrade pip
```

### 패키지 설치 예시
가상환경 내에서 필요한 라이브러리를 설치하면 해당 프로젝트에만 적용됩니다.
```bash
pip install requests  # 예: requests 라이브러리 설치
```

---

## 3. 가상환경 종료 및 관리

* **가상환경 비활성화:** 작업이 끝났다면 아래 명령어로 일반 환경으로 돌아올 수 있습니다.
    ```bash
    deactivate
    ```
* **패키지 목록 저장:** 협업이나 배포를 위해 설치된 패키지 목록을 파일로 저장해두는 것이 관례입니다.
    ```bash
    pip freeze > requirements.txt
    ```
* **패키지 일괄 설치:** 나중에 이 목록을 통해 한 번에 설치할 수 있습니다.
    ```bash
    pip install -r requirements.txt
    ```

---

**주의사항:** Windows PowerShell에서 스크립트 실행 오류(`이 시스템에서 스크립트를 실행할 수 없으므로...`)가 발생할 경우, 관리자 권한으로 터미널을 열고 `Set-ExecutionPolicy RemoteSigned` 명령어를 입력해 권한을 허용해 주어야 합니다.