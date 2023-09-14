---
title: "Moving Data"
layout: default
parent: File Systems
nav_order: 2
---

## File Systems: Moving Data
The login nodes, especially fugg1 and 2, are mostly intended for job submissions.
If you expect to move larger amounts of data, e.g. to a local computer, consider submitting a job that moves the data from a worker node to your system.
This way, you can shift the workload away from login nodes.

Files are usually transferred with `scp` or `rsync`.
