# FPGA-FFT-Implementation
A hardware implementation of the Cooley-Tukey Radix-2 FFT algorithm in FPGA 


## Features
- 32-point to 1024-point configurable FFT
- Radix-2 Decimation-In-Time algorithm
- Pipelined architecture for high throughput
- Verilog/VHDL implementation
- Tested on [Xilinx/Altera] FPGA platforms

## Contents
- [Documentation](#documentation)
- [Installation](#installation)
- [Usage](#usage)
- [Results](#results)
- [References](#references)

## Documentation
See the theory notes(docs/theory.md).

## Installation
1. Clone this repository
2. Setup your FPGA toolchain (Vivado/Quartus)
3. Open the project file in /rtl

## Usage
```verilog
// Instantiate the FFT core
fft_core #(
    .POINT_SIZE(32),
    .DATA_WIDTH(16)
) fft_inst (
    .clk(clk),
    .reset(reset),
    .start(start),
    .data_in(data_in),
    .data_out(data_out),
    .done(done)
);
