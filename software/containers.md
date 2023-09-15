---
title: "Containers"
layout: default
parent: Software on PLEIADES
nav_order: 4
---

## Containers
*Apptainer* (recently *Singularity*) is the most common containerization technology on HPC systems.
Containers are a popular way to control software environments on various different platforms/machines.

With containers you can:
* Create a reproducible version of your research software
* Improve reproducibility of your research
* Run software (mostly) independent from underlying hardware and operating system
* Improve software quality, e.g. through continuous integration


### Different Compiler Versions Using Apptainer 
The container management program singularity is installed on the system. If you need a different compiler version in order to be able to compile your program, you can download them as an image from the docker hub by using the command

```bash
apptainer pull docker://gcc:<version>   # e.g. version 13.2
```

This will create a .sif file in your current directory.
You can then use the command

```bash
apptainer shell <your-.sif-file>   # e.g. gcc_13.2.sif
```

to get an interactive shell using the specified compiler version.
Compile your program in the way you need to and log out of the container in the usual way.


### Apptainer and MPI
Apptainer can be used to execute mpi-processes in a container.
This can be done by using

```bash
mpirun apptainer exec <your-.sif-file> </path/to/program>
```

Note the order of calling mpi and apptainer.
Apptainer will connect the mpi runtime environment on the host system with the mpi processes in the container.

Also see [https://apptainer.org/docs/user/main/mpi.html](https://apptainer.org/docs/user/main/mpi.html) for more on this topic.


### Apptainer and GPUs
You can use GPUs from within a container.
Most important is running a gpu-prepared container gith the `--nv` flag, e.g.:

```bash
apptainer run --nv tensorflow_latest-gpu.sif
```

For more information, please read in the apptainer documentation: [https://apptainer.org/docs/user/main/gpu.html](https://apptainer.org/docs/user/main/gpu.html).
