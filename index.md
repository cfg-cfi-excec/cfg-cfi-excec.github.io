# Comparative Analysis and Enhancement of CFG-based Hardware-Assisted CFI Schemes

This website acts as reference and landing page for our paper on hardware-assisted Control Flow Integrity (CFI) based on Control Flow Graphs (CFGs).
It links to all relevant code repositories containing our implementations and necessary modifications of the RISC-V SoC (PULPissimo) and compiler toolchain (GCC).

## Software

### Compiler

Strictly speaking, there are no changes required for the compiler itself.
However, since the implementations use custom instructions the GNU binutils have to be extended to support them.
Nevertheless, we provide the [**whole toolchain environment**](https://github.com/cfg-cfi-excec/pulp-riscv-gnu-toolchain) to ease building.
This is based on the [PULP toolchain](https://github.com/pulp-platform/pulp-riscv-gnu-toolchain/) which in turn is based on the [official RISC-V toolchain](https://github.com/riscv/riscv-gnu-toolchain).

### Instrumentation/GCC Plugin

The instrumentation of CFI instructions is done by a [**GCC plugin**](https://github.com/cfg-cfi-excec/cfi-gcc-plugin).
To instrument indirect control transfers you have to supply a configuration file that specifies target functions for each call site.
For some CFI schemes the callees have to be assigned labels first:

```
# indirectly called functions
target_file1.c target_function1 label:10
target_file2.c target_function2 label:10

# indirect calls
caller_file.c caller_function line:42 label:10: target_file1.c:target_function1 target_file2.c:target_function2
```

To use the plugin add the respective parameters to the linker step, e.g., `-fplugin=.../gcc_plugin.so -fplugin-arg-gcc_plugin-cfg_file=...txt -fplugin-arg-gcc_plugin-cfi_implementation=EXCEC`.

### Runtime/SDK

For the benchmarks mentioned in the paper we have used the [PULP SDK](https://github.com/pulp-platform/pulp-sdk/tree/v1) with link-time optimization (LTO) enabled to extend instrumentation from just use code also to C library functions but this is not strictly necessary.

## Hardware

We have implemented support for the various CFI schemes in the pipeline of the [CV32E40P RISC-V core](https://github.com/openhwgroup/cv32e40p).
The changes can be seen in our forked repository of [**CV32E40P**](https://github.com/cfg-cfi-excec/cv32e40p).
However, to run evaluations this core has to be integrated into an SoC platform (e.g., [PULPissimo](https://github.com/pulp-platform/pulpissimo/).
To re-build this environment one can use [**our PULPissimo fork**](https://github.com/cfg-cfi-excec/pulpissimo).

## Paper

The paper will be presented at the [CASES](https://esweek.org/cases/) conference during Embedded Systems Week and then published in [Transactions on Embedded Computing Systems (TECS)](https://dl.acm.org/journal/tecs).
The [**preprint**](https://arxiv.org/abs/2103.04456) can be found at arXiv.
