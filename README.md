# FPGA Neural-Network Inference Accelerator

A from-scratch Verilog implementation of a neural-network **inference accelerator**, built up
from a single multiply-accumulate (MAC) unit to a full MNIST digit-recognition datapath on an
Intel **MAX 10** FPGA (DE10-Lite) — with the core MAC engine also submitted to
[TinyTapeout](https://tinytapeout.com/) to be manufactured as a real silicon chip.

> **Status: in progress.** This is an active, solo project. The repository structure and design
> plan are in place; RTL development starts with the MAC unit. See the roadmap below and
> [`docs/project_plan.md`](docs/project_plan.md) for the full plan.

---

## Why this project

A neural network reduces to one operation repeated tens of thousands of times: multiply a set of
inputs by a set of weights and accumulate the results (a MAC). This project designs the digital
hardware that does that fast, from the register-transfer level up — the core skill set behind
ASIC / digital design and AI-hardware roles.

The goal is a hardware artifact I can explain end-to-end and defend line by line: every
architectural decision (bit widths, pipelining, MAC parallelism, on-chip data movement) is my
own, and every module is verified against a known-good reference before it moves forward.

## Architecture (bottom-up)

| Stage | What it is |
|-------|-----------|
| **MAC unit** | A single multiply-accumulate cell — the atomic operation of the whole network. |
| **MAC array** | Multiple MACs in parallel for throughput; on-chip staging and data movement. |
| **Inference datapath** | Real quantized weights + an input image run through the full pipeline. |
| **Board demo** | Design programmed onto MAX 10; result shown over LEDs / UART. |
| **Tapeout** | Core MAC module hardened to the TinyTapeout SKY130 template and submitted for fabrication. |

## Verification strategy

Correctness is proven by **golden-vector co-simulation**: a Python model computes the expected
value at every stage (post-quantization), and the RTL simulation output must match it bit-for-bit.
This is the methodology used throughout — testbenches from the very first MAC unit onward.

## Repository structure

```
rtl/     Verilog source (MAC unit, array, datapath)
sim/     Testbenches and simulation scripts (ModelSim / Questa)
ml/       Python: small MLP training, int8 quantization, golden-vector generation
docs/     Specifications and the full project plan
```

## Tech stack

- **RTL:** Verilog
- **Synthesis / place-and-route:** Quartus Prime Lite (MAX 10)
- **Simulation:** ModelSim-Intel FPGA Starter Edition
- **ML / quantization:** Python (small MLP, int8 quantization, golden-vector generation)
- **Target board:** Intel MAX 10 (DE10-Lite, 10M50)
- **Tapeout:** TinyTapeout, SKY130 process

## Roadmap

- [x] Repository structure and project plan
- [ ] **Stage 1** — MAC unit + testbench, verified in simulation
- [ ] **Stage 2** — MAC array (parallel MACs, on-chip data staging)
- [ ] **Stage 3** — Full inference datapath, bit-exact against the Python golden model
- [ ] **Stage 4** — On-board demo (MAX 10, LED / UART output)
- [ ] **Stage 5** — Tapeout submission (core MAC → TinyTapeout SKY130)

## Notes on tooling

I use AI coding assistants the way I'd use any power tool — scaffolding, boilerplate, and faster
iteration — but the parts that matter (architecture, verification, and debugging) are mine, and I
hold myself to being able to explain every line of RTL in this repo without notes. The design
rationale for that boundary is written up in [`docs/project_plan.md`](docs/project_plan.md).
