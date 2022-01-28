---
title: "Slurm"
---

## Slurm

This is a small getting-started guide about submitting batch jobs with Slurm on the new Pleiades cluster.


### Overview
Previously, we used SGE to submit batch jobs.  
Since many concepts are similar between these kind of tools, you just have to look up the new tool names in many cases. This [rosetta stone](https://slurm.schedmd.com/rosetta.pdf), provided by schedmd is a good starting point.  
A more general introduction to slurm is available at [slurm.schedmd.com/quickstart.html](https://slurm.schedmd.com/quickstart.html).

In the remainder of this article, we will touch on some of the details of the current Slurm configuration. Keep in mind, that we are still tuning the setup, so some details might change in the coming weeks/months. If you have any questions or suggestions, do not hesitate to contact us via [lcg-admin{at}physik.uni-wuppertal.de](javascript:linkTo_UnCryptMailto('nbjmup+mdh.benjoAqiztjl\/voj.xvqqfsubm\/ef');)


### Submitting jobs
There are three login nodes available to submit jobs to the worker nodes: **higgs**, **top** and **fugg1**.
Jobs can run in one out of three partitions, namely:
- **normal** (default), with a time limit of 3 days
- **short**, intended for development and tests, with a time limit of 1 hour
- **long**, with a default time limit of 7 days. Only 30 nodes at a time are allowed to execute in this partition.
- **gpu**, with a default time limit of 3 days. See [Using GPUs](gpu) for more information on how to submit jobs with GPU resources

Think of partitions as a set of worker nodes, which are available to execute your jobs.

Our beegfs (mounted in /beegfs/) serves as a shared resource between the login nodes and the worker nodes.

Here is a small example job, printing the hostnames of multiple worker nodes:

```bash
$ cd /beegfs/
$ cat testjob.sh
#!/bin/sh
#SBATCH --job-name=testjob
#SBATCH --partition=normal
#SBATCH --time=0-0:5:0 # days-hours:minutes:seconds
#SBATCH --nodes=4-10 # at least 4 nodes, up to 10
#SBATCH --mem-per-cpu=128 # in MB
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
wn21009.pleiades.uni-wuppertal.de
wn21010.pleiades.uni-wuppertal.de 
```

It is good practice to provide as much information as possible with each job submission. This way, the scheduler can do a much better job at fitting the workload on available resources. You can personally benefit from this, since your job may run early, even with a lower priority, if it fits in an unclaimed slot!  
For example, consider to provide:

- Number of nodes
- Maximum memory or cores per task
- A reasonable time limit. Otherwise the partition maximum is assumed

Here is a small example for scheduling a MPI job, with a locally compiled OpenMPI 4.1.1 installation.

```bash
$ cat job.sh
#!/bin/sh
/beegfs/<userfolder>/openmpi/install/bin/mpirun ↵
      /beegfs/<userfolder>/openmpi/mpi_hello_world ↵
      --mca btl '^openib'
$ sbatch -N4 job.sh
Submitted batch job 106869
$ cat slurm-106869.out
Hello world from processor wn21050.pleiades.uni-wuppertal.de, ↵
    rank 1 out of 4 processors
Hello world from processor wn21051.pleiades.uni-wuppertal.de, ↵
    rank 2 out of 4 processors
Hello world from processor wn21052.pleiades.uni-wuppertal.de, ↵
    rank 3 out of 4 processors
Hello world from processor wn21049.pleiades.uni-wuppertal.de, ↵
    rank 0 out of 4 processors
$ cd /beegfs/<userfolder>
```

More information about running MPI jobs on Slurm is available at:
- [MPI on Pleiades]({{ site.baseurl }}{% link mpi.md %})
- [https://slurm.schedmd.com/mpi_guide.html#open_mpi](https://slurm.schedmd.com/mpi_guide.html#open_mpi)
- [https://www.open-mpi.org/faq/?category=slurm](https://www.open-mpi.org/faq/?category=slurm)

A single Slurm job can consist of multiple steps. In this case, the salloc command is used to allocate a fixed set of resources and then run multiple "srun -r ..." commands to schedule job steps on these resources:

```bash
$ cd /beegfs/
$ cat testjob_steps.sh
#!/bin/sh
srun -lN2 -r 2 hostname &
srun -lN2 hostname &
sleep 5
squeue -u  -s
wait
$ salloc -N4 testjob_steps.sh
salloc: Granted job allocation 106886
salloc: Waiting for resource configuration
salloc: Nodes wn[21001-21004] are ready for job
1: wn21004.pleiades.uni-wuppertal.de
0: wn21003.pleiades.uni-wuppertal.de
0: wn21001.pleiades.uni-wuppertal.de
1: wn21002.pleiades.uni-wuppertal.de
         STEPID     NAME PARTITION     USER      TIME NODELIST
  106886.extern   extern    normal         0:08 wn[21001-21004]
salloc: Relinquishing job allocation 106886
```

For example, you could use this to execute multiple (different) operations and finally do some clean-up, e.g. to leave your /beegfs area in a consolidated state.

It is also possible to build dependencies between Slurm jobs via the "sbatch --dependency=<dependency_list>" option (also see man sbatch). But a single job with multiple steps has less overhead than multiple jobs and is therefore preferred!


### Fair Share Details
Each user is associated with a account (think: bank-account). Every group has their own account which represent their shares in the cluster. Your job priority is then determined by a fair share algorithm that considers the group-shares, past effective usage of the cluster, job-size and -age. Scheduling large jobs will decrease your personal fair share, compared to your group colleagues, and the group accounts fair share, compared to other institute groups.

This way a fair usage of the resources is ensured, while still utilizing idle cycles as much as possible.

The fair share factor will recover with a half-life of 7 days and all fair share factors are reset at the beginning of the month.

For example, you could use this to execute multiple (different) operations and finally do some clean-up, e.g. to leave your /beegfs area in a consolidated state.

It is also possible to build dependencies between Slurm jobs via the "sbatch --dependency=<dependency_list>" option (also see man sbatch). But a single job with multiple steps has less overhead than multiple jobs and is therefore preferred!


### Useful commands
- **sinfo** - Show current state of worker nodes
- **squeue** - See all running and pending jobs.
- **scancel** - Cancel a specific job
- **sshare** - See current fair share and cluster usage
- **sacct** - Show information about past jobs


### Singularity and Slurm
You can use Singularity in Slurm batch script by just calling the respective command within the script and prepend a srun.  
Note for members of the whep group: submitting from higgs/top might be setting your singularity cache within /common/home, which is not available from the worker nodes. Consider using the SINGULARITY_CACHEDIR environment variable to define a shared location.
