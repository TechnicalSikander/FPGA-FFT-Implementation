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

