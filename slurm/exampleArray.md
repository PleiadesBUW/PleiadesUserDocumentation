---
title: "Job arrays"
layout: default
parent: Slurm
nav_order: 3
---

## Slurm example: Job arrays
Job arrays can be used to submit many (e.g. thousands) similar small/short jobs.
They can reduce the load from the central Slurm controller, so prefer job arrays over many small individual jobs!

A job script could look like this:
```bash
#!/bin/sh
#SBATCH ... # Other options of sbatch

# Execute a single job (this script)
# with 16 steps (0-15)
# But only execute 4 job steps at the same time (%4)
#SBATCH --array=0-15%4

# Each job array step will run with the resource requirements:
#SBATCH -n16    # Each job array step requires 16 tasks
#SBATCH -N1-2   # Each job array step requires 1 or 2 nodes

# You can use %a to have different output files for each job step:
#SBATCH -o jobarraytests_%a.out
#SBATCH -e jobarraytests_%a.err

# Each job array step executes the following
srun hostname
srun echo $SLURM_ARRAY_TASK_ID
srun sleep 10
```

It makes sense to use environment variables like `${SLURM_ARRAY_TASK_ID}`, to decide which arguments should go to which process in the job array.
More details are available at [https://slurm.schedmd.com/job_array.html](https://slurm.schedmd.com/job_array.html).

