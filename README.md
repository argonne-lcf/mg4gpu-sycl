# mg4gpu-sycl: A MadGraph5_aMC@NLO Plugin for GPU Acceleration Using the SYCL Portability Framework

## Introduction

`mg4gpu-sycl` is a Python code generation tool for MadGraph5_aMC@NLO that generates C++ and SYCL code to allow running MadGraph on GPUs. The plugin is designed to simulate processes of arbitrary complexity at both leading and next-to-leading order perturbative accuracy, with or without matching and multi-jet merging to parton showers.

## Installation

To use this plugin, you will need to have the following dependencies installed:

- Python 3.x
- MadGraph5_aMC@NLO
- `gfortran >= 10.2`
- oneAPI DPC++ compiler (or other SYCL implementation)

### Installing MadGraph5_aMC@NLO

1. Clone the `mg5amcnlo` repository from GitHub using the following command:
    ```
    git clone https://github.com/mg5amcnlo/mg5amcnlo.git
    ```
2. Checkout the `gpucpp` branch at commit `6d2182cc1` using the following command:
    ```
    cd mg5amcnlo
    git checkout 6d2182cc1
    ```
3. Export the `mg5amcnlo` directory to the `MG5AMC_HOME` environment variable by adding the following line to your `.bashrc` or `.bash_profile` file:
    ```
    export MG5AMC_HOME=/path/to/mg5amcnlo
    ```
    Make sure to replace `/path/to/mg5amcnlo` with the actual path to your `mg5amcnlo` directory. Note: Executing the above line will add the `MG5AMC_HOME` environment variable to your current environment.

### Installing oneAPI DPC++ compiler

1. Go to the following URL: https://github.com/intel/llvm/blob/sycl/sycl/doc/GetStartedGuide.md
2. Follow the instructions provided in the "Getting Started with oneAPI DPC++" guide to install the oneAPI DPC++ compiler.

### Installing mg4gpu-sycl
Once you have installed the dependencies, you can install the plugin by the following step:

1. Clone the repository to your local machine.
    ```
    git clone https://github.com/argonne-lcf/mg4gpu-sycl.git
    ```

## Usage

To use this plugin, follow these steps:

1. Navigate to the `src` directory of the `mg4gpu-sycl` repository.
2. Execute the bash script `generateAndCompare.sh [--mad] process_name`, where `process_name` is the name of the process you want to test. This script generates two versions of MadGraph: one version tests the matrix element calculation kernels, while the other version produces the full physics workload. The content of the generated code is placed in `mg4gpu-sycl/procs/process_name.sa` for kernel performance testing and in `mg4gpu-sycl/procs/process_name.mad` for running the full physics workload. If you want to generate code for full physics workloads, add `--mad` as an optional argument.
3. Compile and run the generated code on a GPU.

### Compiling and Running the Kernel Performance Testing Code

To compile and run the kernel performance testing code, follow these steps:

1. Navigate to the `SubProcesses` directory of the `process_name.sa` directory.
2. Navigate to one of the subprocess directories named `P*`. The `*` character is used as a placeholder and represents the name of the subprocess you are interested in.
3. Set the `SYCLFLAGS` environment variable to something appropriate for the device you wish to target. For example, when compiling for an NVIDIA A100 GPU, set it using:
    ```
    export SYCLFLAGS="-fsycl -fsycl-targets=nvptx64-nvidia-cuda -Xcuda-ptxas --maxrregcount=255 -Xcuda-ptxas --verbose -Xsycl-target-backend --cuda-gpu-arch=sm_80"
    ```
    Additional details and other compilation flags for targeting other devices can be found in the oneAPI DPC++ Users Manual located at https://github.com/intel/llvm/blob/sycl/sycl/doc/UsersManual.md.
4. Run the following command to compile the kernel performance testing code:
    ```
    make -f sycl.mk build.d_inl0_hrd0/check.exe
    ```
5. Once the compilation is complete, run the following command to execute the kernel performance testing code:
    ```
    ./build.d_inl0_hrd0/check.exe
    ```

    Note that executing the command `./build.d_inl0_hrd0/check.exe` outputs the usage instructions for the executable.

### Compiling and Running the Full Physics Workload Code

To compile and run the full physics workload code, follow these steps:

1. Navigate to the `SubProcesses` directory of the `process_name.mad` directory.
2. Navigate to one of the subprocess directories named `P*`. The `*` character is used as a placeholder and represents the name of the subprocess you are interested in.
3. Set the `SYCLFLAGS` environment variable to something appropriate for the device you wish to target. For example, when compiling for an NVIDIA A100 GPU, set it using:
    ```
    export SYCLFLAGS="-fsycl -fsycl-targets=nvptx64-nvidia-cuda -Xcuda-ptxas --maxrregcount=255 -Xcuda-ptxas --verbose -Xsycl-target-backend --cuda-gpu-arch=sm_80"
    ```
    Additional details and other compilation flags for targeting other devices can be found in the oneAPI DPC++ Users Manual located at https://github.com/intel/llvm/blob/sycl/sycl/doc/UsersManual.md.
4. Run the following command to compile the full physics workload code:
    ```
    make build.d_inl0_hrd0/madevent_sycl
    ```
5. Once the compilation is complete, run one of these commands to execute the full physics workload code:
    * Execute `./build.d_inl0_hrd0/madevent_sycl` and provide additional input when prompted.
    * Execute `./build.d_inl0_hrd0/madevent_sycl < madevent_input`, where `madevent_input` is an input file containing additional input for running the program. Here is an example `madevent_input` file that you can use with this command:

    ```
    1 ! Bridge mode to call cross compiled C++ SYCL code
    16384 ! Number of events calculated per iteration 
    163840 10 5 ! Number of events and max and min iterations
    0.01 ! Precision level cutoff for early exit
    0 ! Grid adjustment
    1 ! Suppress amplitude and use single-diagram enhancement
    0 ! Use exact helicity sum per event
    1 ! Choose single-diagram enhancement for process
    ```
