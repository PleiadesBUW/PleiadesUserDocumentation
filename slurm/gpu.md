---
title: "Using GPUs"
layout: default
parent: Slurm
nav_order: 2
---

> **NOTE:**
> The GPU resources are primarily owned by the `imacm` and `etechnik` groups.
> All other groups are allowed to submit a limited number of jobs with a lower priority to increase utilization.
> We may have to block access to GPU resources by other groups in times of high demand.
> You can check your own group affiliation with `sshare -U`.


There is a general GPU tutorial available at [hpc-wiki.info/hpc/GPU_Tutorial](https://hpc-wiki.info/hpc/GPU_Tutorial).


## Slurm: Using GPUs
This guide is introducing our newly available GPU nodes.
The configuration is still very fresh, so please contact us if there are problems or if you expect something to behave differently.


### Hardware
There are 5 identical GPU nodes available, called `gpu21[001-005]`.
Each node has the following specs:
  - 8 GPUs: NVidia HGX A100
  - 128 Cores: Two sockets with AMD EPYC 7763 64-Core Processors each
  - 2TB Memory, resulting in 16GB per core
  - 14TB of disk space at `/data/`. Please clean up your data when you are done

Certain cores are associated to a certain GPU and can check the affinity between them with `nvidia-smi`:
```
$ srun -N1 -p gpu -A <yourGroupAccount>_gpu -n1 --cpus-per-task 128 --gpus 8 --gpus-per-task 8 nvidia-smi topo -m
        GPU0    GPU1    GPU2    GPU3    GPU4    GPU5    GPU6    GPU7    mlx5_0  CPU Affinity    NUMA Affinity
GPU0     X      NV12    NV12    NV12    NV12    NV12    NV12    NV12    SYS     48-63   3
GPU1    NV12     X      NV12    NV12    NV12    NV12    NV12    NV12    SYS     48-63   3
GPU2    NV12    NV12     X      NV12    NV12    NV12    NV12    NV12    PXB     16-31   1
GPU3    NV12    NV12    NV12     X      NV12    NV12    NV12    NV12    PXB     16-31   1
GPU4    NV12    NV12    NV12    NV12     X      NV12    NV12    NV12    SYS     112-127 7
GPU5    NV12    NV12    NV12    NV12    NV12     X      NV12    NV12    SYS     112-127 7
GPU6    NV12    NV12    NV12    NV12    NV12    NV12     X      NV12    SYS     80-95   5
GPU7    NV12    NV12    NV12    NV12    NV12    NV12    NV12     X      SYS     80-95   5
mlx5_0  SYS     SYS     PXB     PXB     SYS     SYS     SYS     SYS      X

Legend:

  X    = Self
  SYS  = Connection traversing PCIe as well as the SMP interconnect between NUMA nodes (e.g., QPI/UPI)
  NODE = Connection traversing PCIe as well as the interconnect between PCIe Host Bridges within a NUMA node
  PHB  = Connection traversing PCIe as well as a PCIe Host Bridge (typically the CPU)
  PXB  = Connection traversing multiple PCIe bridges (without traversing the PCIe Host Bridge)
  PIX  = Connection traversing at most a single PCIe bridge
  NV#  = Connection traversing a bonded set of # NVLinks
```


### Job Submission and Accounting
The GPU nodes are available in their own `gpu` partition:
```
$ scontrol show partition gpu
PartitionName=gpu
   AllowGroups=ALL AllowAccounts=astro_gpu,cobra_gpu,fugg_gpu,imacm_gpu,lrz_gpu,modellierung_gpu,ops_gpu,optimierung_gpu,risiko_gpu,stroemung_gpu,whep_gpu,zim_gpu AllowQos=ALL
   AllocNodes=ALL Default=NO QoS=N/A
   DefaultTime=NONE DisableRootJobs=NO ExclusiveUser=NO GraceTime=0 Hidden=NO
   MaxNodes=UNLIMITED MaxTime=3-00:00:00 MinNodes=0 LLN=NO MaxCPUsPerNode=UNLIMITED
   Nodes=gpu21[001-005]
   PriorityJobFactor=1 PriorityTier=1 RootOnly=NO ReqResv=NO OverSubscribe=NO
   OverTimeLimit=NONE PreemptMode=OFF
   State=UP TotalCPUs=640 TotalNodes=5 SelectTypeParameters=NONE
   JobDefaults=(null)
   DefMemPerCPU=16000 MaxMemPerCPU=32000
```

As you can see in `AllowAccounts`, we only allow a special purpose Slurm account with a suffix of `_gpu` to submit jobs to the node.
Every user is associated to their groups account **and** a corresponding `_gpu` account.
This way we can have separate fairshare configurations between CPU resources and GPU resources, which might be necessary to prioritize research groups with a larger demand for GPU resources.
You can check the current share distribution with `sshare` yourself.

Slurm treats GPUs as consumable resources and you must specify how many GPUs you would like to request for your job with `--gpus 8`.
Additionally you should decide how many tasks (e.g. processes, option `-n`) you plan to run on a given node and how many `--gpus-per-task` and `--cpus-per-task` you require.
All of these options depend on the scaling behavior of you code and how many CPUs per GPU you need.

To summarize, whenever you intend to submit a job with GPU resources, consider the following options:
  - `-p gpu` to submit to the gpu partition
  - `-A <yourGroupAccount>_gpu` to use your groups special purpose account for the fairshare evaluation
  - Number of nodes, e.g. `-N1`, and number of tasks, e.g. `-n1`
  - `--gpus <N>`, where N is the number of GPUs you require for your job (up to 8 per node)
  - `--gpus-per-task <N>` to set the number of GPUs to be used in a single process/task (up to 8)
  - `--cpus-per-task <N>` to set the number of CPU cores in a single task (up to 128 per node)

Here is an example job script, submitting 8 processes, each with 16 cores and one GPU to a single node:
```
#!/bin/sh
#SBATCH --job-name=gputests
#SBATCH --partition=gpu
#SBATCH --account=<groupname>_gpu
#SBATCH -N 1
#SBATCH --ntasks 8
#SBATCH --cpus-per-task 16
#SBATCH --gpus-per-task 1
#SBATCH --time=0-01:00:00
#SBATCH -o %x-%j.out

srun -n8 nvidia-smi topo -m
```


### Which GPUs is my Job Using?
When you use only a couple of available GPUs on a system, you might wonder **which**.
When submitting your jobs through Slurm, your job will have the `CUDA_VISIBLE_DEVICES` environment variable, which lists your GPU IDs, e.g.:

```
user@gpu21005:~$ env | grep CUDA_VISIBLE
CUDA_VISIBLE_DEVICES=2,6
```

The GPU IDs range from 0 to 7, and match the GPU IDs reported via the `nvidia-smi` tool or in our [monitoring system](../gettingstarted/zabbix).

You may add `echo $CUDA_VISIBLE_DEVICES` to your Slurm job script in order to see which GPUs each job is using.


### Software
Some basic packages are installed on all GPU nodes:
```
# yum list installed | grep nvidia
Loaded plugins: fastestmirror, nvidia
cuda-drivers.x86_64                    495.29.05-1                     @local-centos7.9-x86_64--install-repos-nvidia
kmod-nvidia-latest-dkms.x86_64         3:495.29.05-1.el7               @local-centos7.9-x86_64--install-repos-nvidia
nvidia-driver-latest-dkms.x86_64       3:495.29.05-1.el7               @local-centos7.9-x86_64--install-repos-nvidia
nvidia-driver-latest-dkms-NVML.x86_64  3:495.29.05-1.el7               @local-centos7.9-x86_64--install-repos-nvidia
nvidia-driver-latest-dkms-NvFBCOpenGL.x86_64
                                       3:495.29.05-1.el7               @local-centos7.9-x86_64--install-repos-nvidia
nvidia-driver-latest-dkms-cuda.x86_64  3:495.29.05-1.el7               @local-centos7.9-x86_64--install-repos-nvidia
nvidia-driver-latest-dkms-cuda-libs.x86_64
                                       3:495.29.05-1.el7               @local-centos7.9-x86_64--install-repos-nvidia
nvidia-driver-latest-dkms-devel.x86_64 3:495.29.05-1.el7               @local-centos7.9-x86_64--install-repos-nvidia
nvidia-driver-latest-dkms-libs.x86_64  3:495.29.05-1.el7               @local-centos7.9-x86_64--install-repos-nvidia
nvidia-fabric-manager.x86_64           495.29.05-1                     @local-centos7.9-x86_64--install-repos-nvidia
nvidia-libXNVCtrl.x86_64               3:495.29.05-1.el7               @local-centos7.9-x86_64--install-repos-nvidia
nvidia-libXNVCtrl-devel.x86_64         3:495.29.05-1.el7               @local-centos7.9-x86_64--install-repos-nvidia
nvidia-modprobe-latest-dkms.x86_64     3:495.29.05-1.el7               @local-centos7.9-x86_64--install-repos-nvidia
nvidia-persistenced-latest-dkms.x86_64 3:495.29.05-1.el7               @local-centos7.9-x86_64--install-repos-nvidia
nvidia-settings.x86_64                 3:495.29.05-1.el7               @local-centos7.9-x86_64--install-repos-nvidia
nvidia-xconfig-latest-dkms.x86_64      3:495.29.05-1.el7               @local-centos7.9-x86_64--install-repos-nvidia
yum-plugin-nvidia.noarch               0.5-1.el7                       @local-centos7.9-x86_64--install-repos-nvidia
```

Additionally, you can try some of the available [modules](../software/modules) with GPU related features:
  - `CUDA/11.4.2`
  - `NVHPC/21.7`: The successor of the PGI compilers
  - Various debuggers and profilers, e.g. `TotalView/2021.3.9` and `ARMForge/21.1.1`, as well as Intel parallel studio XE
  - `TensorFlow/2.6.0`

In all of these cases you would run `module load 2021a <module/version>` in your `sbatch` job script or your interactive `salloc`/`srun` allocation to gain access to the corresponding tools.
The modules have not been tested in this context yet, so please get in touch if something does not work as expected.


### Stop GPU Jobs with Low Utilization
You may want to make sure that your GPU job is canceled, if it is not utilizing the GPUs well enough.
With this approach you stop early in case of bugs or other issues, which improves the availability of GPU resources for everyone.

Following the example of a dedicated PLEIADES user, you could create a shell script `monitor_gpu_usage.sh`:
```bash
#! /usr/bin/env bash

# Specify the idle time threshold in seconds (e.g., 1800 seconds for 30 minutes)
IDLE_THRESHOLD=${IDLE_THRESHOLD:-1800}  # Default to 30 minutes if not set

# Function to check GPU usage
check_gpu_usage() {
  local gpu_usage
  # Use CUDA_VISIBLE_DEVICES if set, otherwise fall back to GPU_DEVICE_ORDINAL
  local gpu_list=${CUDA_VISIBLE_DEVICES:-$GPU_DEVICE_ORDINAL}
  
  if [ -z "$gpu_list" ]; then
    echo "No GPUs allocated to this job"
    return 1
  fi

  gpu_usage=$(nvidia-smi --id=$(echo $gpu_list | tr ',' ' ') --query-gpu=utilization.gpu --format=csv,noheader,nounits)
  
  # If all GPUs are below the threshold (e.g., 5%), return 1
  for usage in $gpu_usage; do
    if [ "$usage" -gt 5 ]; then
      return 0
    fi
  done
  return 1
}

# Record the start time
start_time=$(date +%s)

while true; do
  sleep 60  # Check every 60 seconds
  if check_gpu_usage; then
    # If GPUs are not idle, update the start time
    start_time=$(date +%s)
  else
    # Calculate the elapsed time since GPUs became idle
    current_time=$(date +%s)
    elapsed_time=$((current_time - start_time))
    if [ "$elapsed_time" -ge "$IDLE_THRESHOLD" ]; then
      # If the elapsed time exceeds the threshold, exit the job
      echo "GPUs have been idle (below 5% utilization) for more than $((IDLE_THRESHOLD / 60)) minutes. Terminating job."
      scancel $SLURM_JOB_ID
      exit 0
    fi
  fi
done
```

You have to tune the `$IDLE_THRESHOLD` to your workload though!
Downloading or preparing large datasets and similar preparation tasks naturally have low GPU utilization.

In your Slurm job, you could start the script in background and kill it after your main workload is done:
```bash
#! /usr/bin/env bash
#SBATCH -p gpu
#SBATCH ...

# Do preparation tasks with low GPU utilization here
# e.g. Download or prepare some dataset

bash monitor_gpu_usage.sh &   # Start monitor script in background
MONITOR_PID=$!                # Remember PID of monitor script

# Put your work here
some_command                  # main workload
# Anything you wish to do

kill $MONITOR_PID             # Kill monitoring if main workload is done
```
