# Software Information & Scripts for TRX40 Threadripper - AMD Hackintosh Bare Metal

***WARNING - This folder contains untested and experimental scripts and SHOULD NOT be used without understanding the potential risks and implications of making modifications to your system. If you are not comfortable with the risk of completely bricking your Hackintosh, refer to the main EFI. You accept and acknowledge any potential risk that making these changes may bring. You have been warned.***

Below are a list of Software & Applications that are validated to work on this platform, OS version, and potentially required modifications. This is a work in progress as more research is being done, and is in no means, an extensive and comprehensive list.

HS - High Sierra
CA - Catalina
BS - Big Sur
BR - Requires Homebrew
NA - Not Available/Validated at this Time
^ - Indicates Modifications/Workarounds

_Any that don't have tags should work out of the box_

#### Creative

- Adobe Suite [ NA ]
- Figma

#### Benchmarking

- LuxMark
- Cinebench r15/r20
- Geekbench 4/5

#### Development

- Docker [ CA,BR,^ ] _docker-machine version 0.16.2, build bd45ab1_
- VirtualBox [ CA,BR,^ ] _version 6.1.12 r139181 (Qt5.6.3)_
- Parallels Desktop [ CA ] _version 13.1.0_
- Conda/Python

#### Machine Learning

_[ Note ] State of ML on AMD-OSX: ROCm's Developers (AMD's CUDA equivilent) has expressed little interest in supporting macOS/Unix, leaving litte hope of using ROCm-accelerated DL frameworks with AMD GPUs on macOS. Metal API is currently only really supported by TFLite, which for the majority of DL usecases (not Inference), isn't effective. For best performance in production workloads with your chosen framework, it is recommended to rebuild the library from source, as pre-compiled macOS libraries are built for Intel's CPUs. CoreML, which is Apple's latest macOS native ML framework, is heavily focused on inference first, rather than training. Portability between Frameworks is done with ONNX and recompiled into CoreML. At this time, for someone who is looking to leverage their AMD GPU on a AMD Hackintosh, it is not recommended for production ML_

- Tensorflow Native (v1/v2) [ CA ] _CPU Only_
- Tensorflow PlaidML (v1/v2) [ CA ] _GPU with Metal & OpenCL_
- Pytorch Native (Latest) [ CA ] _CPU Only_

After some investigation, it is recommended, if using Conda, to [remove MKL](https://docs.anaconda.com/mkl-optimizations/) (notes below) as a dependency, and use [openBLAS](https://github.com/xianyi/OpenBLAS) built from source. A large majority of python libraries use MKL, including numpy.

Source: [Puget Systems](https://www.pugetsystems.com/labs/hpc/How-To-Use-MKL-with-AMD-Ryzen-and-Threadripper-CPU-s-Effectively-for-Python-Numpy-And-Other-Applications-1637/)

#### Kernel/Library/Extension Level

As all macOS applications are compiled specifically for Intel CPU instruction sets for optimization, this means that often applications may run into kernel panics, crashes, etc. For example, Docker for Desktop (macOS) does not work, but docker-machine does. Docker for Desktop looks for VMX in Intel's CPU instruction sets, which indicate whether virtualization is supported. AMD on the other hand, utilizes svm, an entirely different virtualization instruction set. Therefore, any macOS apps that use Intel specific CPU instructions that are not supported on AMD will run into issues. CLI applications often run less into this problem than Desktop apps, but just remember that the pre-compiled library was likely optimized for Intel.

One specific library issue most AMD Hackintoshes run into is related to Intel's MKL library. Intel's MKL library is found in many graphics-accelerated macOS applications, including Adobe Suite. Kernel panics will pop up when the library detects a non-Intel CPU. Further investigation is needed to solve this problem system-wide.

A temporary workaround is to add an environment variable (~/.zshrc or ~/.profile) to force MKL to not crash with

export MKL_DEBUG_CPU_TYPE=5

Alternatively, rather than displaying AuthenticAMD as the CPU Vendor, masking as GenuineIntel (such as in Virtualized macOS) may resolve a few issues.

***Threadripper's CPU Features (3970X)***

machdep.cpu.features: FPU VME DE PSE TSC MSR PAE MCE CX8 APIC SEP MTRR PGE MCA CMOV PAT PSE36 CLFSH MMX FXSR SSE SSE2 HTT SSE3 PCLMULQDQ MON SSSE3 FMA CX16 SSE4.1 SSE4.2 MOVBE POPCNT AES XSAVE OSXSAVE AVX1.0 RDRAND F16C
machdep.cpu.leaf7_features: RDWRFSGS BMI1 AVX2 SMEP BMI2 PQM PQE RDSEED ADX SMAP CLFSOPT CLWB SHA UMIP RDPID
machdep.cpu.extfeatures: SYSCALL XD 1GBPAGE EM64T LAHF LZCNT PREFETCHW RDTSCP TSCI

