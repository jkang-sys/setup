# VSCode 사용법

## Verilog 세팅

### MacOS + Verilator

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

### Ubuntu + Verilator

`Ctrl + Shift + P` -> `Open User Settings (JSON)`에 아래 내용을 추가합니다. (기존 내용 끝에 쉼표 확인 후 추가)

```json
{
    "verilog.linting.linter": "verilator",
    "verilog.linting.verilator.arguments": "-sv --lint-only -Wall",
    "verilog.linting.verilator.path": "/usr/bin/verilator",
    "verilog.linting.verilator.runAtFileLocation": true,
    "verilog.ctags.path": "/usr/bin/ctags"
}
```

## 기타

* Markdown 미리보기 기본 지원 (Ctrl+Shift+V)