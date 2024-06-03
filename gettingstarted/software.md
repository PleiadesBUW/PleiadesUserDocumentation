---
title: "Software & File Systems"
layout: default
parent: Getting Started
nav_order: 2
---

## Getting Started: Software & File Systems

### Available Software
Our centrally provided software installations are offered through [modules](../software/modules) (also see [hpc-wiki.info/hpc/Modules](https://hpc-wiki.info/hpc/Modules)).
The `LMod` command `module` is used to make certain programs in a specific version available to your current shell.

You can load modules from the [EESSI Projects](https://www.eessi.io/) through `/cvmfs/software.eessi.io`, or local centrally provided modules located in `/beegfs/tools`.
We recommend trying the EESSI approach first, as described in our [Software documentation](../software/modules).

As a general example, to load a GCC 10.3.0 from our local software stack you can run:
```bash
$ module spider GCC/10.3.0
--------------------------------------------
  GCC: GCC/10.3.0
--------------------------------------------
    Description:
      The GNU Compiler Collection includes front ends for C, C++, Objective-C, Fortran, Java, and Ada, as well as libraries for these languages (libstdc++, libgcj,...).


    You will need to load all module(s) on any one of the lines below before the "GCC/10.3.0" module is available to load.

      2021a
[...]
$ module load 2021a GCC/10.3.0
$ gcc --version
gcc (GCC) 10.3.0
Copyright (C) 2020 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE
```

Notice the "2021a" module.
We use a hierarchical module naming scheme, which means that modules only become available, if their dependencies are loaded.
The 2021a module serves as an entry point into a software stack with a collection of many programs in a certain version.
A more recent entry module, e.g. 2022a, is likely to contain more recent versions of the software.
`module spider` will find any version available and using it with `name/version` will tell you exactly how to load the program.

For more information, please consult our [Software documentation](../software).


### File Systems
On our cluster, we have the following storage systems:

* **Workernode local storage** in `/tmp` (**no backup!**): 2TB of fast SSD storage per node. Great place for active work directories in jobs.
* **BeeGFS** in `/beegfs` (**no backup!**): >900TB shared storage accessible in all nodes. Home directory to all users and intended for storing results and organizing data. I/O intensive jobs should avoid on interacting directly with `/beegfs`.
* **CVMFS** in `/cvmfs`: Read-only storage to distribute special software.
* **NFS** in `/common/home` (regular backup): Home directory for users of the whep group

For more information, please consult our [file system documentation](../filesystem).
