---
title: "Slurm example: Clean up tmp directory"
layout: default
parent: Slurm
nav_order: 5
---


## Slurm example: Clean up tmp directory
As mentioned in our [filesystem best practices](../filesystem/bestpractices), it is a good idea to use a local work directory for each job.

In the following minimal example, a local directory is created for the job.
We use the `trap` built-in command to call a `clean_up` function in any case.
This way, the work directory is cleaned up, even if the job fails and the job script is not executed until its end.

```bash
#!/usr/bin/env bash
#SBATCH --nodes 1
#SBATCH --ntasks 1

# Set a specific directory with a job-unique name:
workdir="/tmp/${USER:?}_${SLURM_JOB_ID:?}"
submitdir="${SLURM_SUBMIT_DIR:?}"

mkdir -p "${workdir}"

function clean_up {
    # Leave ${workdir}
    cd "${submitdir}" || exit
    # Use :? to only remove if the variable is defined. Otherwise exit
    rm -rf "${workdir:?}"
    exit
}

# Always call "clean_up" when script ends
# This even executes on job failure/cancellation
trap 'clean_up' EXIT

cd "${workdir}" || exit

# Start real work in workdir
echo "hello world" > "test.log"
cat test.log
sleep 10
```
