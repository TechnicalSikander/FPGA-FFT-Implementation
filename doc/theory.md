# Theory of the Hardware FFT Implementation

## 1. Discrete Fourier Transform (DFT) Basics

The DFT is a linear transformation that converts a discrete-time signal into its frequency components:

$$
X_m = \sum_{n=0}^{N-1} x_n w^{nm} \quad \text{where} \quad w = e^{2i\pi/N}
$$

- $x_n$: Time-domain samples (input)
- $X_m$: Frequency-domain coefficients (output)
- $N$: Number of points (must be power of 2 for Radix-2 FFT)
- $w$: Twiddle factors ("roots of unity")

## 2. Cooley-Tukey Radix-2 Algorithm

### 2.1 Decimation-in-Time (DIT) Approach

The paper implements the DIT FFT which recursively splits the DFT into smaller DFTs:

$$
X_m = \sum_{n=0}^{N/2-1} x_{2n}w^{2nm} + w^m \sum_{n=0}^{N/2-1} x_{2n+1}w^{2nm}
$$

Key properties:
- Divides problem into even and odd indexed samples
- Requires bit-reversed input ordering
- Produces naturally ordered output

### 2.2 Butterfly Operations

The fundamental computation unit:
A' = A + W·B
B' = A - W·B


Where:
- A, B: Input complex numbers
- W: Twiddle factor ($e^{-2πik/N}$)
- A', B': Output complex numbers

## 3. Hardware Implementation Principles

### 3.1 Parallel Processing Advantages

Unlike software FFTs that execute serially, hardware implementations:
- Perform butterfly operations in parallel
- Pipeline arithmetic operations
- Achieve O(N log N) throughput with O(log N) latency

### 3.2 Key Components

1. **Butterfly Processing Unit (BFU)**
   - Complex multiply-accumulate operations
   - Pipeline registers for timing closure
   - 5-bit growth handling (11-bit → 16-bit)

2. **Address Generation Unit (AGU)**
   - Generates data and twiddle factor addresses
   - Implements the rotate-left addressing scheme:
     ```
     m = Rotate(2j, i)
     n = Rotate(2j+1, i)
     ```
   - Controls memory bank switching

3. **Memory Architecture**
   - Ping-pong buffering between two memory banks
   - Dual-port RAM for simultaneous read/write
   - 4-cycle latency compensation

4. **Twiddle Factor Storage**
   - Precomputed roots of unity (W_N^k)
   - Stored in ROM as 16-bit fixed-point values
   - Symmetry exploited to reduce storage

### 3.3 Fixed-Point Arithmetic

Implementation uses Q15 fixed-point format:
- 1 sign bit + 15 fractional bits
- Range: [-1, 1 - 2^-15]
- Multiplication produces 32-bit result
- Truncation to 16 bits after right shift

## 4. Mathematical Optimizations

1. **Twiddle Factor Symmetry**
   - $W_N^{k+N/2} = -W_N^k$
   - Reduces storage by 50%

2. **Bit-Reversed Addressing**
   - Input data must be permuted
   - Achieved by reversing address bits

3. **Pipeline Scheduling**
   - 5-stage butterfly pipeline
   - Careful latency matching between components

## 5. Performance Characteristics

For N-point FFT:
- Operations: ~5N log₂N fixed-point operations
- Memory: 2N complex words (ping-pong buffers)
- Latency: ~log₂N + pipeline_depth cycles
- Throughput: 1 transform per N cycles (fully pipelined)

## 6. References

1. Slade, G. W. (2013). "The Fast Fourier Transform in Hardware: A Tutorial Based on an FPGA Implementation"
2. Cooley, J. W.; Tukey, J. W. (1965). "An algorithm for the machine calculation of complex Fourier series"
3. Oppenheim, A. V.; Schafer, R. W. (1999). "Discrete-Time Signal Processing"

