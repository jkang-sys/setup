# MacOS 환경 Yosys + SKY130 합성

이 가이드는 Apple Silicon/Intel Mac 공통이며, VS Code를 메인 에디터로 사용하는 학생들을 위한 최적의 워크플로우입니다.

---

## 1. MacOS 필수 도구 설치 (Setup)

먼저 터미널에서 `homebrew`를 통해 설계 도구를 설치합니다.

```bash
# 합성 및 시뮬레이션 도구 설치
brew install yosys iverilog verilator

# 추가적으로 파이프라인 처리에 필요한 도구들
brew install gawk pkg-config graphviz

```

Python 가상환경으로 진입합니다.


```bash

# Python 환경에서 volare 설치
pip3 install volare cocotb pytest

# PDK가 저장될 경로 설정 (예: ~/pdk)
export PDK_ROOT=$HOME/pdk
mkdir -p $PDK_ROOT

# Sky130 PDK의 최신 안정 버전을 리스트업하고 설치
volare ls-remote --pdk sky130

# Sky130 PDK 다운로드 (volare 이용)
# 특정 버전을 선택해서 설치 (가장 위쪽의 최신 hash 권장)

# volare enable --pdk sky130 <version_hash>
volare enable --pdk sky130 005958b70402e5a1ee3b05b382d216535959a888 # 특정 버전 예시

```

---

## 2. 프로젝트 파일 구성 (Coding)

VS Code에서 프로젝트 폴더를 만들고 아래 두 파일을 작성합니다.

### 2.1 RTL 설계 (`counter.v`)

```verilog
module counter (
    input clk,
    input reset,
    output reg [3:0] count
);
    // 비동기 리셋을 가진 4비트 카운터
    always @(posedge clk or posedge reset) begin
        if (reset)
            count <= 4'b0000;
        else
            count <= count + 1'b1;
    end
endmodule
```

### 2.2 합성 스크립트 (`synth.ys`)
Yosys가 Sky130 게이트로 변환하기 위한 상세 레시피입니다.
```tcl
# 1. 설계 읽기
read_verilog counter.v
hierarchy -check -top counter

# 2. 고수준 합성 (추상 연산자 생성)
proc; opt; fsm; opt; memory; opt

# 3. Sky130 라이브러리 로드 (경로 주의!)
# volare로 설치 시 보통 아래 경로에 위치합니다.
read_liberty -lib ~/pdk/sky130A/libs.ref/sky130_fd_sc_hd/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# 4. 기술 매핑 (Technology Mapping) - 핵심 단계
techmap
# D-플립플롭을 실제 Sky130 셀로 매핑
dfflibmap -liberty ~/pdk/sky130A/libs.ref/sky130_fd_sc_hd/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
# 조합 논리(Gate)를 Sky130 셀로 매핑
abc -liberty ~/pdk/sky130A/libs.ref/sky130_fd_sc_hd/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# 5. 최적화 및 정리
opt; clean

# 6. 리포트 생성 (결과 파일 저장)
tee -o counter.area.log stat -liberty ~/pdk/sky130A/libs.ref/sky130_fd_sc_hd/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
tee -o counter.timing.log sta

# 7. 최종 네트리스트 출력
write_verilog counter_netlist.v
```

---

## 3. 실행 및 결과 분석 (Synthesis)

터미널에서 합성을 실행합니다.

```bash
yosys -s synth.ys
```

### 결과 로그 확인 (`counter.area.log`)
합성이 성공하면 다음과 같은 정보가 포함되어야 합니다.
* **Cell 이름**: `sky130_fd_sc_hd__dfxtp_1` 등 (성공 시) / `$add`, `$adff` (실패 시)
* **Chip Area**: 전체 로직이 차지하는 실제 면적 ($\mu m^2$)



---

## 4. 전문가 가이드: 왜 이렇게 하나요?

1.  **왜 `techmap`과 `abc`를 따로 하나요?**
    * Yosys는 범용 도구입니다. `techmap`은 `$add` 같은 거대한 연산자를 낱개 게이트로 쪼개고, `abc`는 그 게이트들을 Sky130이라는 특정 공정의 "표준 셀(Standard Cell)" 규격에 딱 맞춰서 다시 조립하는 역할을 합니다.
2.  **왜 `.lib` 파일을 계속 참조하나요?**
    * `.lib` 파일 안에는 각 게이트의 **면적(Area)**과 **지연 시간(Delay)** 정보가 들어있습니다. 이 정보가 없으면 Yosys는 타이밍 분석과 면적 계산을 할 수 없습니다.
3.  **MacOS 특이점**
    * `synth_sky130` 같은 통합 명령어가 안 먹힐 때는 위와 같이 **Manual Flow**를 사용하는 것이 가장 확실하며, 이는 실제 현업에서 스크립트를 커스터마이징하는 방식과 동일합니다.

---

## 5. 다음 단계 제언

합성된 `counter_netlist.v`는 이제 단순한 코드가 아니라 **실제 반도체 회로도**입니다. 
* **Cocotb 연동**: 이 네트리스트를 Cocotb로 시뮬레이션하여, 실제 게이트 지연이 포함된 상태에서도 카운터가 잘 동작하는지 확인(Post-Synthesis Simulation)하는 과정을 추천합니다.

**여기까지 진행하면서 `counter.area.log`에 숫자가 잘 찍히는지 확인해 보셨나요?**