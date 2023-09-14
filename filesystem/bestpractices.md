---
title: "Best Practices"
layout: default
parent: File Systems
nav_order: 3
---

## File Systems: Best Practices

> **Note:**
>
> Your files on BeeGFS are not backed up!
> Make sure to regularly store important results and data on another device.
> In case of a catastrophic failure or event, we won't be able to restore the data.


## BeeGFS and /tmp
Many and frequent accesses to files on `/beegfs` can produce significant load on the metadata servers.
In consequence, the responsiveness of the shared filesystem goes bad for all users.
There are a couple of things you should avoid when working on `/beegfs`, because of this:
* **Too many files in a single directory**. Each directory is managed by one of the metadata servers and having very many files (e.g. 1000+) in one directory can result in unbalanced blocking operations, if the directory is used in your jobs.
* Frequent lookups in a directory, e.g. through `ls` or implicitly through any `readdir` operation in your program or programming language. The lookup results in a locked operations in the metadata server. This could happen if you frequently check for file status in your job scripts.
* Starting many short running processes (seconds), with the software installed on BeeGFS. Each process is creating a new data stream to read the program data, which can overwhelm the storage system.
* Using `/beegfs` as your working directory for frequent file I/O in your job. Please consider using the local `/tmp` storage. Every worker node is equipped with fast 2TB SSDs for exactly this purpose.
  * Afterwards you can transfer your results to a permanent storage on `/beegfs`
  * If you want to store logfiles etc., consider packing everything in a `.tar` file, since a single large file is better to digest on a parallel filesystem than many small files
  * If you use /tmp in your jobs, please make sure that you clean up the directories you created. Also consider what happens to these files, if your job gets canceled or crashes.

We have a [job script example](../slurm/exampleTmp) that automatically cleans up the `/tmp` directory at the end of a job.

If left files in `/tmp` that you want to rescue or remove manually, the best approach is to book an interactive shell on the node via:
```bash
srun -p short -w wn21053 -n1 -t 60 --pty /bin/bash
```
