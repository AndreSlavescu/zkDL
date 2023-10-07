![logo](./images/logo.png)
# zkDL: Zero-knowledge proofs of deep learning on CUDA
![Version](https://img.shields.io/badge/Version-v1.0.0-blue) [![License](https://img.shields.io/badge/License-BSD_2--Clause-orange.svg)](https://opensource.org/licenses/BSD-2-Clause) [![Maintenance](https://img.shields.io/badge/Maintained%3F-yes-green.svg)](https://github.com/SafeAILab/zkdl/issues) [![Contributions welcome](https://img.shields.io/badge/Contributions-welcome-brightgreen.svg?style=flat)](https://github.com/SafeAILab/zkdl/pulls)

**zkDL** is a specialized backend that provides zero-knowledge proofs (ZKP) for deep learning, powered by CUDA.

![highlight](./images/highlight.png)

## Latest News
- [ ] [2023/10/1] v1.0.0 is released. New features:
	- The first-known zkML platform runnable on GPUs.
		- New CUDA backend for arithmetic operations in the finite field and elliptic curve, which might be of independent interest to the ZKP community more broadly.
 	- Supporting ZKP for MLP with customized width and depth (other architectures will be coming soon). Allowing users to load their own NNs from PyTorch (.pt file) for ZKP.
  	- Achieving 100x to 1000x speedup in the standard Benchmark.

---

## Introduction

**zkDL** represents a significant step in integrating zero-knowledge proofs with deep learning. It uniquely emphasizes the preservation of tensor structures and harnesses the parallel processing power of CUDA, resulting in efficient proof computations.

## Benchmarking

We assessed zkDL using the benchmark set by [ModulusLab](https://drive.google.com/file/d/1tylpowpaqcOhKQtYolPlqvx6R2Gv4IzE/view), which focuses on verifiable inference across fully connected neural networks of diverse scales (with up to 18M parameters). Results running on a single NVIDIA Tesla A100 GPU indicated that zkDL is capable of accelerating proving time by magnitudes of 100x to 1000x.

<div align="center">
	<img src="./images/benchmark.png" alt="Editor" width="700">
</div>

## Technical Overview

- **Foundation**: This project is based on the CUDA implementation of the `bls12-381` elliptic curve, using the `ec-gpu` package developed by Filecoin.
  
- **Quantization**: For the efficient application of ZKP tools, the floating-point numbers involved in deep learning computations are quantized.
    
- **Tensor Structures and GKR Protocol**: We utilize a specialized version of the GKR protocol to maintain tensor structures, facilitating the parallelization of proofs. For operations like ReLU, which are inherently non-arithmetic and thus challenging for ZKP schemes, *auxiliary inputs* are employed to transition them into arithmetic operations.

- **Neural Network Modelling**: We fit the neural network into the ZKP backend by modelling it as an arithmetic circuit. Our strategy breaks free from the conventional layer-wise precedence, especially when non-arithmetic operations come into play, allowing for a more efficient 'flattened' circuit representation.

## Prerequisites

Ensure CUDA is installed on your system, and identify the compatible CUDA architecture. For this documentation, we use the `sm_70` architecture as an example.

## Setup & Installation

1. Set the architecture using NVCC_FLAGS in the `Makefile`:

```cmake
# NVCC compiler flags
NVCC_FLAGS := -arch=sm_70
```

2. Compile the demonstration:

```bash
make demo
```

Note that it is typical for the compilation to take some time, possibly up to a few minutes. We are actively working on streamlining this process.

## Running the Demo

To initiate the demo:

```bash
# ./demo batch_size input_dim hidden_dim hiddem_dim ... hidden_dim output_dim
./demo 64 784 1000 1773 1773 1773 1773 1773 1124 1000
```
This command will run an inference on a fully connected ReLU neural network with 8 layers and ~18M parameters, with input dimension 784, output dimension 1000, and hidden dimensions 1773 (with the exception of 1000 for the first, and 1124 for the last), and a batch size of 64. This neural network aligns with the size of the [largest benchmark](#benchmarking). The entire process, including initialization, should conclude in a few seconds. The proving time is expected to be 0.075-0.15 seconds on a modern GPU, depending on the performance of different servers.

## Future Development

- [ ] Broaden the range of supported structures and back propagations to increase adaptability.

- [ ] Re-introduce zero-knowledge verifiable **training** alongside **inference**, as detailed in [zkDL: Efficient Zero-Knowledge Proofs of Deep Learning Training](https://arxiv.org/abs/2307.16273).

- [ ] Implement proof compression across deep learning layers and explore a multi-GPU version for enhanced performance.

## License
The license for this zkML tool is BSD-2-Clause.

## Contribution
As an open-source project in a dynamically evolving field, we wholeheartedly welcome contributions, whether they take the shape of novel features, enhanced infrastructure, or improved documentation.

For those interested in contributing to zkDL or seeking further information, please submit a pull request or reach out to Haochen Sun at haochen.sun@uwaterloo.ca. If you find this repository useful, please consider giving ⭐.

