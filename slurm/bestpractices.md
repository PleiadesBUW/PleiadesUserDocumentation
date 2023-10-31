---
title: "Best Practices"
layout: default
parent: Slurm
nav_order: 7
---

## Slurm: Best Practise
## Shellcheck
[shellcheck.net](https://shellcheck.net/) is very useful to highlight problems with your shell scripts!
You can use it to check and improve your job scripts.


## Other Best Practise
Every job submission in Slurm introduces some overhead to the batch system.
If you have many short jobs of the same kind, e.g. 2000 x 30 minutes, you should combine your workload in fewer submission scripts or consider using Slurms **job arrays** (check [our example job](exampleArray)).
This way you bundle all of these jobs in a single job submission, but still can treat the items individually as job steps.

Please try to estimate a maximum execution time and set your job time limits accordingly.
  * Specify it with `-t`, shorter than the partitions maximum time
  * This will also increase the likelihood of your job starting early!
  * For longer job times than three days, **if your program regularly saves states/snapshots**:
    * Schedule a first job to the normal partition
    * Use `sbatch -d <jobid>` to set a job dependency to the original job, to continue processing
  * As an exception, the `long` partition allows for longer jobs, but there are a couple of risks involved:
    * Jobs naturally have to wait longer to start, since other (long) jobs may block the partition resources
    * A power or cooling failure might kill your jobs prematurely, wasting all of the computation and energy up to that point
    * Planned maintenance has to wait longer until your jobs are finished. In case of critical security interventions we might be even forced to kill you jobs.
    * All issues are mitigated if your programs have some form of checkpointing to save intermediate states. In this case, use the normal partition as described above.


### Coming from a different scheduler
Since many concepts are similar between different scheduler implementations, you just have to look up the new tool names in many cases. This [rosetta stone](https://slurm.schedmd.com/rosetta.pdf), provided by SchedMD is a good starting point.  
