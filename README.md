# 基于开源 cpu 扩展自定义指令实现 RISC-V AI神经网络推理并且将其可视化

RISC-V 自定义指令（MAC/RELU）的神经网络推理验证 demo。在 RTL 仿真中跑通推理后，用这个页面可视化每一步的计算过程。

## 做了什么

在 RV32I 五级流水线 CPU 上扩展了 4 条自定义指令，全部使用 RISC-V 规范预留的 custom-0 opcode（`7'b0001011`），R-type 格式：

| 指令 | funct3 | 格式 | 功能 |
|------|--------|------|------|
| `mac rd, rs1, rs2` | `000` | R-type | `rd = rs1 * rs2`，单周期有符号乘法 |
| `clz rd, rs1` | `001` | R-type | `rd = clz(rs1)`，前导零计数 |
| `clip rd, rs1, rs2` | `010` | R-type | `rd = clip(rs1, rs2[4:0], rs2[9:5])`，钳位 |
| `relu rd, rs1` | `011` | R-type | `rd = max(rs1, 0)`，ReLU 激活 |

编码示例（GNU assembler 不支持这些指令，汇编中用 `.word` 硬编码）：
```
mac  x3, x10, x12  → 0x00C5018B
relu x3, x4        → 0x0002318B
```

硬件实现：
- `inst_decoder.v` — 译码阶段识别 custom-0，输出 `custom_flags[3:0]`
- `ex_custom.v` — 新增执行单元，纯组合逻辑，单周期完成
- `cpu_pipeline.v` — 流水线集成 + 前推旁路

用这两条指令手写了一个 2→2 全连接神经网络，在 RTL 仿真（iverilog）里跑推理，结果写入 GPIO 寄存器。仿真输出 y0=10，与软件预期一致。

## CPU 源码

CPU 核心RTL 来自开源项目：项目开源地址： https://github.com/renyangang/riscv-mcu 

本项目包含可视化页面和配套的汇编测试程序。后续可能开源更多自己的改动，包括基于开源cpu，对其涉及到的总线进行重构，采用AHB-lite总线以及APB总线，以及扩展的4条指令的实现相关涉及代码。

## 文件

- `index.html` — 推理过程可视化页面（双击打开）
- `test_nn.s` — 运行在 CPU 上的 NN 推理汇编代码

## 打开方式

直接访问 https://yoriliu1.github.io/nn-viz

或者下载 `index.html` 用浏览器打开。
