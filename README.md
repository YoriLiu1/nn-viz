# RISC-V AI 神经网络推理可视化

RISC-V 自定义指令（MAC/RELU）的神经网络推理验证 demo。硬件上跑通推理后，用这个页面可视化每一步的计算过程。

## 做了什么

在 RV32I 五级流水线 CPU 上扩展了 custom-0 自定义指令：

- **MAC**：单周期有符号乘法，`rd = rs1 * rs2`
- **RELU**：单周期 ReLU 激活，`rd = max(rs1, 0)`

用这两条指令手写了一个 2→2 全连接神经网络，在仿真里跑推理，结果写入 GPIO。硬件输出 y0=10，与软件预期一致。

## CPU 源码

CPU RTL 不归我所有，原始项目见：

> https://github.com/[原作者]/[仓库名]（待补充）

本项目只包含可视化页面和配套的汇编测试程序。后续可能开源更多自己的改动。

## 文件

- `index.html` — 推理过程可视化页面（双击打开）
- `test_nn.s` — 运行在 CPU 上的 NN 推理汇编代码

## 打开方式

直接访问 https://yoriliu1.github.io/nn-viz

或者下载 `index.html` 用浏览器打开。
