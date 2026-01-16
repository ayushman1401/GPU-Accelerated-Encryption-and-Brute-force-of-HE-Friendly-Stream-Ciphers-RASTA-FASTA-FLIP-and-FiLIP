# GPU-Accelerated Evaluation and Cryptanalysis of HE-Friendly Stream Ciphers

This repository contains the full experimental framework used to evaluate the **performance
and cryptanalytic implications of HE-friendly stream ciphers** on modern CPU and GPU
architectures. The work focuses on four representative designs:

- **RASTA**
- **FASTA**
- **FLIP**
- **FiLIP**

and studies both **honest-use encryption performance** and **adversarial reduced-key
exhaustive search** under a unified, reproducible methodology.

The results support an academic study targeting **high-assurance cryptographic
evaluation** in the style of *IACR Transactions on Cryptographic Hardware and Embedded
Systems (TCHES)*, with particular emphasis on:

- Bit-level parallelism  
- Scalability  
- Hardware-aware security  
- Concrete attack cost  

---

## What We Have Done

We implemented, benchmarked, and analyzed multiple **CPU and GPU implementations**
of RASTA, FASTA, FLIP, and FiLIP, covering both **encryption throughput** and
**reduced-key key-recovery attacks**.

Specifically, we:

- Implemented **encryption (keystream generation)** for:
  - FASTA
  - RASTA
  - FLIP–128
  - FiLIP–144
- Developed **bit-exact CPU and GPU implementations** sharing identical:
  - Public-parameter generation
  - Round / filter logic
  - Validation procedures
- Implemented **reduced-key exhaustive search attacks** for all four ciphers
- Measured real runtimes for feasible parameter sizes
- **Extrapolated performance** to cryptographically relevant regimes, including:
  - Keystream length \( N \rightarrow 2^{128} \)
  - Unknown key bits \( u = 128 \)
- Compared architectural trade-offs across:
  - Multi-core CPU parallelism
  - GPU instruction-level parallelism (ILP)
  - Grid-stride and multi-guess GPU kernels
  - Shared-memory and hybrid bit-sliced designs

All results are presented in **LaTeX-ready tables and plots**, with measured and
extrapolated data clearly distinguished.

---

## Why We Did This

HE-friendly stream ciphers are designed to minimize **multiplicative depth** and
**non-linear operations**, making them attractive for **homomorphic encryption (HE)**
and **transciphering**. However, these same structural choices—low algebraic degree,
bit-level operations, and public randomness—may significantly affect **concrete security
under parallel attack models**.

Encryption efficiency alone does not determine real-world security. A cipher that is
efficient for homomorphic evaluation may still be vulnerable if **key recovery scales
efficiently on massively parallel hardware**, such as GPUs.

Our goals were therefore to:

- Quantify honest encryption throughput on CPUs and GPUs
- Measure and compare reduced-key attack cost across different cipher families
- Understand how cipher structure (ASASA vs. filter–permutator) affects GPU acceleration
- Evaluate whether HE-friendly designs unintentionally favor attackers with commodity GPUs
- Provide **hardware-aware security insights**, not just asymptotic complexity estimates

This work bridges the gap between **theoretical HE-oriented cipher design** and
**practical, hardware-aware cryptanalysis**.

---

## How We Did It

### 1. CPU Implementations

For each cipher, we implemented:

- **Serial CPU baselines**, to establish reference performance
- **Parallel CPU implementations**, using multi-threading to exploit available cores

These implementations serve as:

- A baseline for honest encryption cost
- A reference attacker model without specialized hardware

---

### 2. GPU Implementations

We designed multiple **CUDA kernels (via Numba)** to explore optimization dimensions
common across all ciphers:

- **Straightforward kernels**
  - One block or key guess per thread
- **Instruction-Level Parallelism (ILP)**
  - Loop unrolling and operation reordering to increase arithmetic density
- **Multi-Guess / Grid-Stride kernels**
  - Each thread processes multiple counters or key guesses to amortize overhead
- **Shared-Memory hybrids**
  - Cache immutable key material or public parameters in shared memory
- **Hybrid Bit-Sliced kernels**
  - Evaluate multiple independent instances or key guesses in lock-step using word-level masks

These designs allow us to isolate which **architectural features** most strongly influence
performance across different cipher structures.

---

### 3. Encryption Throughput Measurement

For encryption, we measured keystream throughput (in Gbit/s) as a function of keystream
length \( N \):

- Measurements span \( N = 2^{12} \) to \( 2^{24} \)
- Throughput stabilizes once kernel-launch overhead is amortized
- GPU performance reaches a **steady-state plateau**, indicating compute- or memory-bound behavior
- CPU performance remains significantly lower across all ciphers

Extrapolated results confirm that **steady-state throughput is independent of keystream
length**, validating the extrapolation methodology.

---

### 4. Reduced-Key Recovery Experiments

For cryptanalysis, we performed **reduced-key exhaustive search**:

- The adversary is assumed to know all but \( u \) key bits
- Measured runtimes for representative values of \( u \)
- Extrapolated results up to \( u = 128 \)

We evaluate a wide range of attack strategies, including:

- Basic brute force
- ILP-unrolled kernels
- Prefix-sieve pruning
- Persistent GPU work queues
- Two-stage GPU sieve-and-verify pipelines
- Hybrid GPU-prefix + CPU-verification approaches
- Hybrid bit-sliced exhaustive search

This enables **realistic attack-cost estimation** under different hardware assumptions.

---

### 5. Extrapolation Methodology

Because full cryptographic parameters are infeasible to test directly:

- We identify steady-state **linear regimes** (encryption) and **exponential regimes** (attacks)
- Estimate effective throughput or key-testing rates
- Extrapolate conservatively to large-scale parameters

Measured and extrapolated results are always shown separately to avoid overstating conclusions.

---

## Novelty and Contributions

This work provides several key contributions:

- **First unified GPU study of HE-friendly stream ciphers**
  - Covering RASTA, FASTA, FLIP, and FiLIP under identical conditions
- **Cross-family comparison**
  - Reveals how ASASA-style designs and filter–permutator constructions differ under GPU acceleration
- **Bit-sliced GPU cryptanalysis**
  - Demonstrates that several HE-friendly designs strongly benefit from bit-sliced attack strategies
- **Attack-centric evaluation**
  - Goes beyond encryption benchmarks to quantify adversarial advantage
- **Measured-to-extrapolated linkage**
  - Explicitly validates extrapolation assumptions using real data
- **Concrete security insights**
  - Shows that commodity GPUs significantly reduce exhaustive-search cost even when asymptotic complexity is unchanged

These insights are directly relevant for **cipher designers**, **security evaluators**, and
**standardization efforts** involving HE-friendly symmetric primitives.

---

## Disclaimer

This repository contains **research code for benchmarking and cryptanalysis**.
It is **not intended for production use**.

---
