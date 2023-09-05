# mg4gpu-sycl: A MadGraph5_aMC@NLO Plugin for GPU Acceleration Using the SYCL Portability Framework

## Introduction

`mg4gpu-sycl` is a Python code generation tool for MadGraph5_aMC@NLO that generates C++ and SYCL code to allow running MadGraph on GPUs. The plugin is designed to simulate processes of arbitrary complexity at both leading and next-to-leading order perturbative accuracy, with or without matching and multi-jet merging to parton showers.

## Installation

To use this plugin, you will need to have the following dependencies installed:

- Python 3.x
- MadGraph5_aMC@NLO `git clone -b gpucpp https://github.com/mg5amcnlo/mg5amcnlo.git ${MG5AMC_HOME}; cd ${MG5AMC_HOME}; git checkout 6d2182cc1`
- C++ compiler
- SYCL compiler

Once you have installed the dependencies, you can install the plugin by following these steps:

1. Clone the repository to your local machine.
2. Run the `setup.py` script to install the plugin.

## Usage

To use this plugin, follow these steps:

1. Navigate to the `src` directory of the `mg4gpu-sycl` repository.
2. Execute the bash script `generateAndCompare.sh [--mad] process_name`, where `process_name` is the name of the process you want to test. This script generates two versions of MadGraph: one version tests the matrix element calculation kernels, while the other version produces the full physics workload. The content of the generated code is placed in `mg4gpu-sycl/procs/process_name.sa` for kernel performance testing and in `mg4gpu-sycl/procs/process_name.mad` for running the full physics workload. If you want to generate code for full physics workload, add `--mad` as an optional argument.
3. Compile and run the generated code on a GPU.
