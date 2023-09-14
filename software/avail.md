---
title: "Available Software"
layout: default
parent: Software on PLEIADES
nav_order: 1
---

## Software: Available Software
We offer many software installations through [modules]({{ site.baseurl }}{% link software/modules.md %}).
Alternatively, you can source an LCG release, as described on the module page.
Finally, you can use [Singularity]({{ site.baseurl }}{% link software/containers.md %}) to set up a specific software environment by running your job in a container.

### List of Special Software
  - CUDA
    - `module load 2021a CUDA/11.4.2`
  - NVHPC
    - `module load 2021a NVHPC/21.7`
  - TotalView: Debugger and analyzer
    - `module load 2021a TotalView/2021.3.9`
  - ARMForge: Debugger and analyzer
    - `module load 2021a ARMForge/21.1.1`
  - NAG Library through modules `NAG` and `NAGfor`
    - `module load 2021a intel-compilers/2021.2.0 NAG/27.3.0`
    - `module load 2021a NAGfor/7.1.01`
    - `module load 2022a intel-compilers/2021.4.0 NAG/29.0.0`
    - `module load 2022a NAGfor/7.1.14`
    - When using NAG, you have to source `${EBROOTNAG}/nll6i29dbl/scripts/nagvars.sh` with the correct arguments for your situation, e.g. "int64 vendor static"
  - Intel parallel studio XE 2020
    - Contains compilers, MPI, libraries and profiling tools like VTune Amplifier, Advisor, etc.
    - **Parallel studio is superseded by oneAPI modules** Example modules: `intel-compilers`, `impi`, `imkl`, `VTune`
    - If you want to set up the old version, go with `source /beegfs/Tools/intel/setup.sh`
  - [PGI]({{ site.baseurl }}{% link software/pgi.md %})
    - Collection of special purpose compilers for heterogeneous environments (CPUs & GPUs)
    - **PGI is superseded by the [NVHPC module]({{ site.baseurl }}{% link software/modules.md %}), but both approaches should work.**
  - COMSOL 6, limited to certain groups
