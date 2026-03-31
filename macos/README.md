# Mac OS 환경설정

---

# 🚀 MacOS VLSI 설계 및 검증 환경 구축 가이드

본 문서는 **Apple Silicon(M1/M2/M3)** 환경에서 `VSCode`, `Verilator`, `iverilog`, `cocotb`를 이용한 설계 환경 구축 요약본입니다.

## 1. 터미널 환경 설정 (Prerequisites)

### 📦 패키지 설치 (Homebrew)
```bash
# 시뮬레이터 및 분석 툴 설치
brew install icarus-verilog verilator gtkwave universal-ctags

# VSCode 터미널 명령어 등록 (이미 등록했다면 생략 가능)
# VSCode에서 Cmd+Shift+P -> 'Shell Command: Install code command in PATH' 실행
```

### 🐍 Python 가상환경 구축
```bash
# 프로젝트 루트에서 실행
python3 -m venv .vhdl_env
source .vhdl_env/bin/activate

# 필수 라이브러리 설치
pip install cocotb cocotb-test pytest
```

---

## 2. VSCode 설정 (`settings.json`)

`Cmd + Shift + P` -> `Open User Settings (JSON)`에 아래 내용을 추가합니다. (기존 내용 끝에 쉼표 확인 후 추가)

```json
{
    "verilog.linting.linter": "verilator",
    "verilog.linting.verilator.arguments": "-sv --lint-only -Wall",
    "verilog.linting.verilator.path": "/opt/homebrew/bin/verilator",
    "verilog.linting.verilator.runAtFileLocation": true,
    "verilog.ctags.path": "/opt/homebrew/bin/ctags"
}
```
> **주의**: `Verilog/SystemVerilog Tools` 확장 프로그램은 `mshr-h`와 충돌하므로 삭제하세요.

---

## 3. 핵심 코드 템플릿 (FIFO 16x16 예제)

### 🔹 RTL: `fifo.v`
```verilog
module fifo (
    input  logic        clk,
    input  logic        rst_n,
    input  logic [15:0] din,
    input  logic        wr_en,
    input  logic        rd_en,
    output logic [15:0] dout,
    output logic        full,
    output logic        empty
);
    // TODO: Internal Logic Implementation

    // Waveform Dump for GTKWave
    `ifdef COCOTB_SIM
    initial begin
        $dumpfile("dump.vcd");
        $dumpvars(0, fifo);
        #1;
    end
    `endif
endmodule
```

### 🔹 Testbench: `test_fifo.py`
```python
import cocotb
from cocotb.clock import Clock
from cocotb.triggers import FallingEdge, RisingEdge, Timer

async def reset_dut(dut):
    dut.rst_n.value = 0
    await Timer(20, units="ns")
    dut.rst_n.value = 1
    await RisingEdge(dut.clk)

@cocotb.test()
async def fifo_basic_test(dut):
    # 100MHz Clock
    cocotb.start_soon(Clock(dut.clk, 10, units="ns").start())
    
    # Reset
    await reset_dut(dut)
    
    # Write Operation
    dut.din.value = 0xABCD
    dut.wr_en.value = 1
    await RisingEdge(dut.clk)
    dut.wr_en.value = 0
    
    # Read Operation & Verify
    dut.rd_en.value = 1
    await RisingEdge(dut.clk)
    await FallingEdge(dut.clk) # Data stable point
    
    assert int(dut.dout.value) == 0xABCD, f"Mismatch: {hex(int(dut.dout.value))}"
    dut._log.info("FIFO Basic Test Passed!")
```

### 🔹 Automation: `Makefile`
```makefile
SIM ?= icarus
TOPLEVEL_LANG ?= verilog
VERILOG_SOURCES += $(PWD)/fifo.v
TOPLEVEL = fifo
MODULE = test_fifo

ifeq ($(SIM), verilator)
    EXTRA_ARGS += --trace --trace-structs -Wall
endif

include $(shell cocotb-config --makefiles)/Makefile.sim

wave:
	gtkwave dump.vcd
```

---

## 4. 실행 워크플로우

1.  **시뮬레이션 실행**: `make SIM=icarus` (또는 `make SIM=verilator`)
2.  **파형 분석**: `make wave`
3.  **정리**: `make clean`

---

