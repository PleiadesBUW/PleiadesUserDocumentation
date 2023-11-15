---
title: "BeeGFS"
layout: default
parent: File Systems
nav_order: 1
---

## BeeGFS
A [BeeGFS](https://www.beegfs.io/) cluster file system is installed on fugg1.pleiades.uni-wuppertal.de login machine and on all cluster worker nodes, i.e. the file system is shared among all nodes and can be used to develop code and to save output files from cluster jobs.
In general, no „copy constructions“ are needed.
A group quota according to the share of each participating group has been applied.
The quota can be checked in our [monitoring system](../gettingstarted/zabbix) or with `beegfs-ctl`:
```
beegfs-ctl --getquota --gid <groupname>

Quota information for storage pool Default (ID: 1):

      user/group     ||           size          ||    chunk files    
     name     |  id  ||    used    |    hard    ||  used   |  hard   
--------------|------||------------|------------||---------|---------
   <groupname>| <gid>||   51.50 TiB|   60.00 TiB|| 12345678|unlimited
```

You find your home directory at:

```bash
/beegfs/USERNAME
```

**Heads up!**
There is no backup for the /beegfs file system. However, this file system is running on raid systems.

if you need real local space on the worker nodes, use „/tmp“, but please clean up inside your jobs scripts, otherwise you will overload the nodes.
We provide an [example job script](../slurm/exampleTmp) that automatically cleans up your files in `/tmp` in any case.


**Heads up! WHEP users**

You have a different home directory (`/common/home/USERNAME`).

Hence, you need to have the same files in
```bash
/common/home/USERNAME/.ssh
```
and

```bash
/beegfs/USERNAME/.ssh
```

This can easily be achieved by copying the whole directory.
