---
title: "Available Software"
layout: default
parent: Software on PLEIADES
nav_order: 1
---

## Software: Available Software
We offer many software installations through [modules](../software/modules).
You can load modules from the [EESSI Projects](https://www.eessi.io/) through `/cvmfs/software.eessi.io`, or local centrally provided modules located in `/beegfs/tools`.
Alternatively, you can source an LCG release, as described on the module page.
Finally, you can use [Apptainer containers](../software/containers) to set up a specific software environment by running your job in a container.

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
    - `module load 2022a intel-compilers/2021.4.0 NAG/29.0.0`
      - When using NAG, you have to source `${EBROOTNAG}/nll6i29dbl/scripts/nagvars.sh` with the correct arguments for your situation, e.g. "int64 vendor static"
    - `module load 2021a NAGfor/7.1.01`
    - `module load 2022a NAGfor/7.1.14`
    - `module load 2022a NAGfor/7.2.3`
    - `module load 2022a intel-compilers/2021.4.0 NAG/30.0.0`
      - When using NAG, you have to source `${EBROOTNAG}/nll6i30dbl/scripts/nagvars.sh` with the correct arguments for your situation, e.g. "int64 vendor static"
  - Intel parallel studio XE 2020
    - Contains compilers, MPI, libraries and profiling tools like VTune Amplifier, Advisor, etc.
    - **Parallel studio is superseded by oneAPI modules** Example modules: `intel-compilers`, `impi`, `imkl`, `VTune`
    - If you want to set up the old version, go with `source /beegfs/Tools/intel/setup.sh`
  - [PGI](../software/pgi)
    - Collection of special purpose compilers for heterogeneous environments (CPUs & GPUs)
    - **PGI is superseded by the [NVHPC module](../software/modules), but both approaches should work.**
  - COMSOL 6, limited to certain groups. If you are interested in using COMSOL, [join our rocket chat room (chat.uni-wuppertal.de)](https://go.rocket.chat/invite?host=chat.uni-wuppertal.de&path=invite%2FDbyQJk).
