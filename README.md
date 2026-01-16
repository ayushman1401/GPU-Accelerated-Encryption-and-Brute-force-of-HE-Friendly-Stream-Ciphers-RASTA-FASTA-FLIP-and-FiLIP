# GPU-Accelerated Evaluation and Cryptanalysis of FLIP

This repository contains the full experimental framework used to evaluate the **performance and cryptanalytic implications of the FLIP cipher** on modern CPU and GPU architectures. The work focuses on two complementary aspects:

1. **Encryption throughput** of FLIP under different parallelization strategies  
2. **Reduced-key exhaustive search (key recovery)** performance under CPU and GPU acceleration  

The results support an academic study targeting high-assurance cryptographic evaluation (IACR TCHES style), with an emphasis on *bit-level parallelism*, *scalability*, and *practical attack cost*.

---

## What We Have Done

We implemented, benchmarked, and analyzed multiple **CPU and GPU implementations of FLIP**, covering both **honest-use performance (encryption)** and **adversarial performance (reduced-key recovery)**.

Specifically, we:

- Implemented **FLIP encryption** on:
  - CPU (serial and parallel)
  - GPU using multiple kernel designs
- Implemented **reduced-key exhaustive search attacks** on FLIP-128
- Measured real runtimes for feasible parameter sizes
- **Extrapolated** performance to cryptographically relevant sizes (e.g., `N → 2^128`, `u = 128`)
- Compared architectural trade-offs between:
  - CPU parallelism
  - GPU instruction-level parallelism (ILP)
  - GPU multi-guess strategies
  - GPU shared-memory and bit-sliced hybrids

All results are presented both numerically (tables) and visually (plots) in LaTeX-ready form.

---

## Why We Did This

FLIP is designed around **bit-level operations**, which makes it a strong candidate for:
- Hardware efficiency
- SIMD-style execution
- GPU acceleration

However, **encryption efficiency alone does not determine security**. A cipher that is slow to encrypt may still be vulnerable if **key recovery scales well on parallel hardware**.

Our goals were therefore to:

- Understand **where FLIP bottlenecks actually lie**
- Quantify **how much GPUs reduce the cost of exhaustive search**
- Evaluate whether FLIP’s structure unintentionally favors attackers with massively parallel hardware
- Provide **realistic attack cost estimates**, not just asymptotic complexity

This bridges the gap between *theoretical cryptanalysis* and *practical, hardware-aware security evaluation*.

---

## How We Did It

### 1. CPU Implementations

We implemented FLIP on the CPU in:
- **Serial mode**, as a baseline
- **Parallel mode**, using multi-threading to exploit available cores

These implementations establish:
- A reference point for honest encryption cost
- A lower bound on attacker performance without specialized hardware

---

### 2. GPU Implementations

Multiple GPU kernels were designed to explore different optimization dimensions:

- **Straightforward GPU kernel**  
  Direct mapping of FLIP operations to GPU threads

- **Instruction-Level Parallelism (ILP)**  
  Unrolling and reordering operations to maximize pipeline utilization

- **Multi-Guess Kernel**  
  Evaluates multiple key guesses per thread to amortize memory and control overhead

- **Shared-Memory Hybrid Kernel**  
  Uses shared memory to reduce global memory latency

- **Bit-Sliced Hybrid Kernel**  
  Exploits FLIP’s bit-oriented structure to maximize parallelism across word lanes

These designs allow us to isolate which architectural features most strongly influence performance.

---

### 3. Encryption Throughput Measurement

For encryption, we measured throughput (in Gbit/s) across increasing keystream sizes `N`.

Key observations:
- Throughput stabilizes quickly once kernel-launch overhead is amortized
- GPU performance reaches a **flat plateau**, indicating compute-bound behavior
- CPU performance remains an order of magnitude lower

Extrapolated measurements confirm that **steady-state throughput is independent of keystream length**, validating the extrapolation methodology.

---

### 4. Reduced-Key Recovery Experiments

For cryptanalysis, we performed **reduced-key exhaustive search** on FLIP-128:
- Measured runtimes for representative numbers of unknown key bits `u`
- Extrapolated to the full key size (`u = 128`)

This allows us to estimate **realistic attack costs** under different hardware assumptions.

---

### 5. Extrapolation Methodology

Because full-size parameters are infeasible to test directly:
- We measured performance in the linear/exponential regime
- Identified steady-state slopes
- Extrapolated conservatively to cryptographic scales

Both measured and extrapolated results are shown explicitly to avoid misleading conclusions.

---

## Novelty and Contributions

This work provides several **novel contributions**:

1. **First systematic GPU study of FLIP**
   - Including multiple kernel designs rather than a single “best-effort” implementation

2. **Bit-sliced GPU cryptanalysis**
   - Demonstrates that FLIP’s bit-level structure strongly favors bit-sliced attackers

3. **Attack-centric evaluation**
   - Goes beyond encryption benchmarks to quantify *adversarial advantage*

4. **Measured-to-extrapolated linkage**
   - Explicitly validates extrapolation assumptions using real data

5. **Constant-factor security insights**
   - Shows that GPU acceleration significantly lowers exhaustive search cost even when asymptotic complexity is unchanged

These insights are directly relevant for cipher designers, evaluators, and standardization bodies.

---

