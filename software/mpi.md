---
title: "MPI on PLEIADES"
layout: default
parent: Software on PLEIADES
nav_order: 3
---

> **Note (2024-06-03):**
>
> Since the update to Alma Linux 9, we do not recommend using `srun` to manage MPI jobs anymore!
> You can just use `mpirun` / `mpiexec` in your Slurm job scripts.
> We are following the recommendations of [OpenMPI](https://docs.open-mpi.org/en/v5.0.x/launching-apps/slurm.html#using-slurm-s-direct-launch-functionality)
> This likely applies to other MPI implementations as well.


## Software: MPI on PLEIADES
(Also see [hpc-wiki.info/hpc/MPI](https://hpc-wiki.info/hpc/MPI))

There are multiple MPI environments available:
1. OpenMPI4: `module load 2021a GCC/10.3.0 OpenMPI/4.1.1` (and other versions, see `module spider OpenMPI`)
1. Intel MPI: `module load 2021a iimpi/2021a` (oneAPI) or through Parallel Studio with `source /beegfs/Tools/intel/setup.sh` (Version from 2020)
1. Local package OpenMPI3 in `/lib64/openmpi3` on our worker nodes
1. Compiling your own MPI libraries
1. LCG release (See last section of [modules](../software/modules), **not using InfiniBand**)

All MPI versions were tested on PLEIADES with an MPI benchmark.
These tests covered the mpirun and srun approach (see below), as well as ethernet and infiniband communication.

Many problems with MPI are caused by a mismatch between the applications expected MPI version/configuration and the used MPI version in your environment.
If you experience problems, try a clean build and investigate MPI related options during your application build and at runtime.

**When compiling your own MPI**, make sure to send a build job to the worker nodes and provide the `--with-ucx` flag (and possibly more!).
Otherwise your MPI version is likely to not utilize our InfiniBand network and rely on ethernet communication instead.


###  Using MPI in Slurm Jobs
There are two approaches to use MPI in your Slurm batch scripts:
1. `mpirun`/`mpiexec`
2. `srun` (**not recommended anymore!**)

Deciding on the number of nodes, processes and cores per process can confusing sometimes.
Use `srun --cpu-bind=help` to show available options to bind CPU resources managed by Slurm to your (MPI-)processes and have a look at the [Slurm CPU Management guide](https://slurm.schedmd.com/cpu_management.html).

Applications that implicitly ship MPI may need additional configuration, e.g. enabling slurm support or pointing to PMI libraries.
This is a case-by-case situation where you should study the corresponding documentation of your application.

Alternative PMI options can be listed with `srun --mpi=list`, but they are not well supported anymore.
Consider reading the [Slurm MPI documentation](https://slurm.schedmd.com/mpi_guide.html).


### Example Job Script with OpenMPI 4
```bash
#!/bin/bash

#SBATCH -p short
#SBATCH -t 60
#SBATCH -N 4 # 4 Nodes
#SBATCH -n 4 # 4 processes in total

module load 2021a GCC/10.3.0 OpenMPI/4.1.1

# Option one:
mpirun /path/to/mpiapplication <arguments>

# Or use srun (not recommended anymore! pmix support likely missing!)
srun --mpi=pmix_v3 /path/to/mpiapplication <arguments>
```

It is possible to pass `--mca` options in these commands as well.


### Notes on Intel MPI
```bash
#!/bin/bash

#SBATCH -p short
#SBATCH -t 60
#SBATCH -N 4 # 4 Nodes
#SBATCH -n 4 # 4 processes in total

# Chose ONE of the following:
# Use recent version of oneAPI impi
module load 2021a iimpi/2021a
# Alternatively use impi shipped with Parallel Studio 2020
source /beegfs/Tools/intel/setup.sh

# Tell impi where to pick up the PMI library, paths above.
# Maybe try libpmix.so or libpmi2.so, if you have problems
export I_MPI_PMI_LIBRARY=/usr/lib64/libpmi.so

# Using mpirun directly:
mpirun /path/to/mpiapplication <arguments>
# Or through the hydra process manager
mpiexec.hydra -bootstrap slurm -n <num_procs> /path/to/mpiapplication <arguments>
# srun instead (not recommended anymore!)
srun --mpi=pmix_v3 /path/to/mpiapplication <arguments>
```


### Local OpenMPI 3
If you need more information about the locally installed OpenMPI 3 version, you can look around a worker node by
```bash
# Look into local openmpi3 manually on WN
$ salloc -N1 -n1
$ ssh wn21123 # replace with correct wn number of your interaction session
$ yum info openmpi3
$ ls /lib64/openmpi3
$ <ctrl-d to exit salloc>
```

Alternatively, if you are just interested in file locations:
```bash
$ srun -N1 -n1 tree /lib64/openmpi3 | less
```
