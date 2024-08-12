---
title: "Computations"
layout: default
parent: Getting Started
nav_order: 3
---

## Getting Started: Computations

### Job Submission
Job submission is done through the [Slurm](../slurm) batch system (also see [hpc-wiki.info/hpc/SLURM](https://hpc-wiki.info/hpc/SLURM)).

Jobs are usually submitted via a job script and the `sbatch` command, for example:
```bash
$ cd /beegfs/${USER}
$ cat testjob.sh
#!/bin/sh
#SBATCH --job-name=testjob
#SBATCH --partition=short
#SBATCH --time=0-0:5:0    # days-hours:minutes:seconds
#SBATCH --nodes=4-8       # at least 4 nodes, up to 8
#SBATCH --ntasks=8        # 16 processes
#SBATCH --mem-per-cpu=128 # in MB, up to 3800MB per core
srun hostname | sort

$ sbatch testjob.sh
Submitted batch job 106867

$ cat slurm-106867.out
wn21001.pleiades.uni-wuppertal.de
wn21002.pleiades.uni-wuppertal.de
wn21003.pleiades.uni-wuppertal.de
wn21004.pleiades.uni-wuppertal.de
wn21005.pleiades.uni-wuppertal.de
wn21006.pleiades.uni-wuppertal.de
wn21007.pleiades.uni-wuppertal.de
wn21008.pleiades.uni-wuppertal.de
```

It is also possible to start an interactive job with:
```
$ srun -p short -N1 -n1 --pty /bin/bash
srun: job 12040877 queued and waiting for resources
srun: job 12040877 has been allocated resources
$     # shell now on worker node.
```

For more information, please refer to the more detailed [Slurm documentation](../slurm).
