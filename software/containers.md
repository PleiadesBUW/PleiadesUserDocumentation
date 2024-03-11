---
title: "Containers"
layout: default
parent: Software on PLEIADES
nav_order: 4
---

## Containers
*Apptainer* (until recently *Singularity*) is the most common containerization technology on HPC systems.
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
Apptainer can be used to execute MPI-processes in a container.
This can be done by using

```bash
mpirun apptainer exec <your-.sif-file> </path/to/program>
```

Note the order of calling MPI and apptainer.
Apptainer will connect the MPI runtime environment on the host system with the MPI processes in the container.

Also see [https://apptainer.org/docs/user/main/mpi.html](https://apptainer.org/docs/user/main/mpi.html) for more on this topic.


### Apptainer and GPUs
You can use GPUs from within a container.
Most important is running a gpu-prepared container with the `--nv` flag, e.g.:

```bash
apptainer run --nv tensorflow_latest-gpu.sif
```

For more information, please read in the apptainer documentation: [https://apptainer.org/docs/user/main/gpu.html](https://apptainer.org/docs/user/main/gpu.html).

### CentOS 7 Environment with /cvmfs using Apptainer
If you need a CentOS 7 environment on any machine that runs AlmaLinux 9, you can use Apptainer to create a container with CentOS 7 setup. To do so, first, download the CentOS 7 image from the Docker Hub by using the command:

```bash
apptainer pull docker://centos:7
```
This will create a Singularity Image Format (SIF) file named `centos_7.sif` in your directory.

**Opening an Interactive Shell in the CentOS 7 Container:**
 You can explore and execute commands within the CentOS 7 container by opening an interactive shell. Use the following command:
```bash
apptainer shell centos_7.sif
```

**Mounting /cvmfs Inside an Apptainer Container:** To access CernVM-FS (`/cvmfs`) from within the container, bind-mount the host `/cvmfs` directory inside the container. Open an interactive shell with the mounted `/cvmfs` by using the command:
```bash
apptainer shell --bind /cvmfs:/cvmfs centos_7.sif
```

**Running a Slurm Job with CentOS 7 and CVMFS:** To submit a Slurm job with CentOS 7 and CVMFS (e.g., `centos_cvmfs_job.sh`) , you can create a script with the necessary configurations. For example:

```bash
#!/bin/bash
#SBATCH --job-name=centos_cvmfs_job
#SBATCH --partition=normal
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=00:30:00

apptainer exec --bind /cvmfs:/cvmfs /path/to/centos_7.sif /path/to/executable.sh
```
Before submitting the job to the Slurm scheduler, ensure the executable has executable permissions:

```bash
chmod +x /path/to/executable.sh
```

A simple `executable.sh` can be:
```bash
echo "Hello from within the CentOS 7 container!"
echo "CVMFS contents in /cvmfs/:"
ls -l /cvmfs/atlas.cern.ch
```

Now, you can submit the job:
```bash
sbatch centos_cvmfs_job.sh
```
