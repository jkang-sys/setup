# WSL 개발환경 설정

**WSL + Ubuntu 24.04 + 개발환경 + GitHub + Python venv**까지 한 번에 재현 가능한 형태로 정리해드리겠습니다.
(실제로 다시 세팅할 때 그대로 따라칠 수 있는 “실전 스크립트 + 절차” 기준)

---

# 0) 전체 아키텍처

* OS: Windows + WSL2
* Linux: Ubuntu 24.04
* 개발 위치: `~/work`
* Git: SSH (443 우회)
* IDE: VS Code (Remote-WSL)
* Python: venv 기반

---

# 1) WSL + Ubuntu 24.04 설치

### PowerShell (관리자)

```powershell
wsl --install -d Ubuntu-24.04
```

---

# 2) Ubuntu 초기 세팅

```bash
sudo apt update && sudo apt -y upgrade

sudo apt -y install \
  build-essential git curl wget unzip zip \
  ca-certificates gnupg \
  pkg-config cmake ninja-build \
  python3 python3-pip python3-venv \
  clang gdb \
  openssh-client \
  tmux htop tree ripgrep
```

---

# 3) GUI 및 EDA 환경

```bash
sudo apt -y install \
  gnome-terminal \
  xterm \
  x11-utils x11-apps dbus-x11 \
  gtkwave verilator iverilog
```

---

# 4) 작업 디렉토리 구성

```bash
mkdir -p ~/work ~/tools ~/.venvs
cd ~/work
```

---

# 5) VS Code 연동

### Windows에서

* VS Code 설치
* Extension: **Remote - WSL**

### WSL에서

```bash
cd ~/work/REPO
code .
```
---

# 6) 핵심 베스트 프랙티스 (중요)

### 반드시 지킬 것

* 프로젝트는 **~/work**에 위치
* `/mnt/c`에서 빌드/venv 금지
* venv에서는 `sudo` 사용 금지
* Git은 SSH 사용

### 권장

* `.venv`는 프로젝트 내부에
* VS Code는 Remote-WSL 사용
* xterm 유지 (EDA 호환)
