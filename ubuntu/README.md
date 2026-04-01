# Ubuntu 24.04 LTS 환경설정

가장 먼저 환경 구축의 기반이 되는 **Conda(Miniconda)** 설치와 설정부터 차근차근 진행해 봅시다. Conda는 Python 패키지(특히 Cocotb)와 시스템 라이브러리 간의 의존성 충돌을 방지하는 아주 강력한 도구입니다.

---

## 1. Miniconda 설치하기
무거운 Anaconda보다는 필요한 패키지만 골라 담을 수 있는 **Miniconda**를 추천합니다. 터미널(`Ctrl+Alt+T`)을 열고 다음 명령어를 순서대로 입력하세요.

### 1-1. 설치 스크립트 다운로드 및 실행
```bash
# 최신 Miniconda 설치 스크립트 다운로드
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh

# 스크립트 실행 (화면에 나오는 지시사항에 따라 Enter 또는 'yes' 입력)
bash Miniconda3-latest-Linux-x86_64.sh
```

### 1-2. 변경사항 적용 및 확인
설치가 완료되면 쉘을 재시작하거나 아래 명령어로 설정을 즉시 반영합니다.
```bash
source ~/.bashrc

# 설치 확인 (버전 정보가 나오면 성공입니다)
conda --version
```

---

## 2. VLSI 설계 전용 가상환경 생성
설계 프로젝트마다 독립된 환경을 유지하는 것이 좋습니다. 여기서는 `vlsi_env`라는 이름으로 환경을 만들고, 우리가 사용할 핵심 도구들을 설치하겠습니다.

### 2-1. 가상환경 생성 및 활성화
```bash
# Python 3.10 버전으로 환경 생성
conda create -n vlsi_env python=3.10 -y

# 환경 활성화
conda activate vlsi_env
```

### 2-2. 필수 도구 설치 (Icarus Verilog & Cocotb)
Verilator는 보통 시스템 라이브러리로 직접 빌드하는 것이 성능상 유리하지만, `iverilog`와 `cocotb`는 Conda를 통해 쉽게 관리할 수 있습니다.

```bash
# Icarus Verilog 및 필수 빌드 도구 설치
conda install -c conda-forge iverilog make gcc_linux-64 gxx_linux-64 -y

# Cocotb 설치 (Python 기반 테스트벤치 프레임워크)
pip install cocotb cocotb-test pytest
```

---

## 3. VS Code와 Conda 연동 설정
우리의 메인 에디터인 VS Code에서 이 가상환경을 인식하도록 설정해야 합니다.

1.  **VS Code 실행**: 터미널에 `code .`을 입력하세요.
2.  **Extension 설치**: 왼쪽 마켓플레이스 아이콘을 클릭하고 다음 확장을 설치합니다.
    * **Python**: Microsoft 제공
    * **Verilog-HDL/SystemVerilog**: mshr-h 제공 (Linter로 활용)
3.  **인터프리터 선택**: `Ctrl + Shift + P`를 누르고 `Python: Select Interpreter`를 검색합니다. 방금 만든 `vlsi_env`를 선택하세요.

---

## 4. 환경 테스트 (간단한 코드 확인)
설정이 잘 되었는지 확인하기 위해 간단한 Verilog 코드와 이를 테스트할 Cocotb 구조를 예시로 들어보겠습니다.

### [File 1: adder.v]
가장 기본적인 반가산기 코드입니다.
```verilog
module adder (
    input  wire a,
    input  wire b,
    output wire sum,
    output wire carry
);
    assign sum   = a ^ b;
    assign carry = a & b;
endmodule
```

### [File 2: test_adder.py (Cocotb)]
Python으로 작성된 테스트벤치입니다. 이 코드가 실행되려면 앞서 설치한 `cocotb` 환경이 활성화되어 있어야 합니다.
```python
import cocotb
from cocotb.triggers import Timer

@cocotb.test()
async def adder_basic_test(dut):
    """Test for 1 + 1"""
    dut.a.value = 1
    dut.b.value = 1
    
    await Timer(2, units="ns")
    
    assert dut.sum.value == 0, f"Error: sum is {dut.sum.value}"
    assert dut.carry.value == 1, f"Error: carry is {dut.carry.value}"
    print("Test Passed!")
```

---

## 5. 꿀팁: 터미널 시작 시 자동 활성화 방지
Conda를 설치하면 터미널을 켤 때마다 `(base)` 환경이 자동으로 켜집니다. 시스템 기본 환경을 깨끗하게 유지하고 싶다면 아래 설정을 추천합니다.

```bash
# 자동 활성화 끄기
conda config --set auto_activate_base false
```
이제 터미널을 켜도 기본 상태를 유지하며, 작업을 시작할 때만 `conda activate vlsi_env`를 입력하면 됩니다.


Conda를 사용하다 보면 가상환경이 어디에 저장되는지, 그리고 용량 관리나 백업을 위해 위치를 변경할 수 있는지 궁금해지는 시점이 반드시 옵니다. VLSI 설계 툴들은 라이브러리가 무거운 경우가 많아 위치 관리가 중요하죠.

---

## 6. 기본 저장 위치 (Default Path)

Ubuntu에서 Miniconda를 기본 설정으로 설치했다면, 가상환경은 보통 아래 경로에 생성됩니다.

* **기본 경로:** `~/miniconda3/envs/`
* **활성화된 환경 확인 방법:** 터미널에서 다음 명령어를 입력하면 현재 생성된 모든 가상환경의 경로를 확인할 수 있습니다.

```bash
conda env list
# 또는
conda info --envs
```
출력 결과에서 **별표(*)**가 표시된 줄이 현재 사용 중인 환경이며, 그 오른쪽에 실제 물리적 경로가 나타납니다.

---

## 7. 특정 위치에 가상환경 만들기

프로젝트 폴더 내부에 환경을 격리하고 싶거나, 용량 문제로 HDD 등 다른 파티션에 설치해야 할 때는 `-p` (prefix) 옵션을 사용합니다.

```bash
# 현재 디렉토리의 .venv 폴더에 환경 생성
conda create -p ./.venv python=3.10

# 활성화할 때는 이름 대신 경로 사용
conda activate ./.venv
```

---

## 8. Conda 설정 변경하기 (저장 위치 고정)

앞으로 생성할 모든 환경이 특정 위치(예: 용량이 큰 별도 하드디스크)에 저장되길 원한다면 `.condarc` 파일을 수정해야 합니다.

### 8-1. 현재 설정 확인
```bash
conda config --show envs_dirs
```

### 8-2. 경로 추가
예를 들어, `/media/data/conda_envs`라는 디렉토리를 기본 저장소로 지정하고 싶다면:
```bash
conda config --add envs_dirs /media/data/conda_envs
```
이렇게 설정하면 `conda create -n [이름]` 명령을 실행할 때 상단에 추가된 경로에 우선적으로 환경이 생성됩니다.

---

## 9. 용량 관리 꿀팁 (VLSI 설계자용)

Conda 환경을 여러 개 만들다 보면 **Index, Cache** 파일들이 수십 GB를 차지하게 됩니다. 특히 시뮬레이션 환경을 자주 변경한다면 주기적으로 아래 명령어를 실행해 주세요.

```bash
# 사용하지 않는 패키지 및 캐시 삭제
conda clean --all
```

> **전문가의 조언:** > VLSI 설계 환경에서는 `verilator`나 `iverilog` 같은 바이너리 툴과 `cocotb` 같은 Python 라이브러리가 섞여 있습니다. 가상환경 위치가 꼬이면 `PYTHONPATH` 에러가 발생할 수 있으니, 가급적 **기본 경로**를 사용하되 용량이 부족하다면 Miniconda 설치 자체를 용량이 큰 파티션에 진행하는 것이 가장 깔끔합니다.

