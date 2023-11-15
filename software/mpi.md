---
title: "MPI on PLEIADES"
layout: default
parent: Software on PLEIADES
nav_order: 3
---

## Software: MPI on PLEIADES
There are multiple MPI environments available:
1. OpenMPI4: `module load 2021a GCC/10.3.0 OpenMPI/4.1.1`
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

### PMI Library
The PMI library acts as an interface between Slurm and various MPI versions.
This way Slurm can manage MPI processes, if the MPI library has been compiled correctly, e.g. by using `--with-pmi` and `--with-slurm`.
If you intend to compile your own MPI versions, you may have to mention the location of PMI libraries:
```
# find /lib64/ -name libpmi*
/lib64/libpmi.so.1.0.1
/lib64/libpmi.la
/lib64/libpmi.so
/lib64/libpmi2.la
/lib64/libpmi2.so
/lib64/libpmi.so.1
/lib64/libpmi2.so.1
/lib64/libpmi2.so.1.0.0
/lib64/libpmix.la
/lib64/libpmix.so
/lib64/libpmix.so.2.2.32
/lib64/libpmix.so.2
```

These paths may also become relevant if you use Intel MPI.


###  Using MPI in Slurm Jobs
There are two approaches to use MPI in your Slurm batch scripts:
1. `srun`
2. `mpirun`

`srun` is by default executing `srun --mpi=pmix_v3`, which may require your software to be build against PMIx, available as mentioned above.
Alternative PMI options can be listed with `srun --mpi=list`

Applications that implicitly ship MPI may need additional configuration, e.g. enabling slurm support or pointing to PMI libraries.
This is a case-by-case situation where you should study the corresponding documentation of your application.

Consider reading the [Slurm MPI documentation](https://slurm.schedmd.com/mpi_guide.html).

Deciding on the number of nodes, processes and cores per process can confusing sometimes.
Use `srun --cpu-bind=help` to show available options to bind CPU resources managed by Slurm to your (MPI-)processes and have a look at the [Slurm CPU Management guide](https://slurm.schedmd.com/cpu_management.html).


### Example Job Script with OpenMPI 4
```bash
#!/bin/bash

#SBATCH -p short
#SBATCH -t 60
#SBATCH -N 4 # 4 Nodes
#SBATCH -n 4 # 4 processes in total

module load 2021a GCC/10.3.0 OpenMPI/4.1.1

# Option one:
srun --mpi=pmix_v3 /path/to/mpiapplication <arguments>

# Or using mpirun directly:
mpirun /path/to/mpiapplication <arguments>
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

# Option one:
srun --mpi=pmix_v3 /path/to/mpiapplication <arguments>
# Or using mpirun directly:
mpirun /path/to/mpiapplication <arguments>
# Third option through the hydra process manager
mpiexec.hydra -bootstrap slurm -n <num_procs> /path/to/mpiapplication <arguments>
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
