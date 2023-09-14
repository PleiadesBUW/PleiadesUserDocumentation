---
title: "Containers"
layout: default
parent: Software on PLEIADES
nav_order: 4
---

## Software: Containers
Apptainer (recently Singularity), is the most common containerization technology on HPC systems.

### Different compiler versions using singularity
The container management program singularity is installed on the system. If you need a different compiler version in order to be able to compile your program, you can download them as an image from the docker hub by using the command

```bash
singularity pull docker://gcc:<version>
```

This will create a .sif file in your current directory. You can then use the command

```bash
singularity shell <your-.sif-file>
```

to get an interactive shell using the specified compiler version. Compile your program in the way you need to and log out of the container in the usual way.


### Singularity and MPI
Singularity can be used to execute mpi-processes in a container. This can be done by using

```bash
mpirun singularity exec <your-.sif-file> </path/to/program> 
```

Note the order of calling mpi and singularity. Singularity will connect the mpi runtime environment on the host system with the mpi processes in the container.

