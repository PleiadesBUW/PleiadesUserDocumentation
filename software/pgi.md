---
title: "PGI"
layout: default
parent: Software on PLEIADES
nav_order: 5
---

**PGI is superseded by the [NVHPC module](../software/modules), but both approaches should work.**

## Software: PGI
PGI provides tools to develop programs in a heterogeneous environment (CPUs & GPUs):

- Fortran, C++17, C compilers
- CUDA Fortran
- OpenMP 4.5 (multicore CPUs)
- OpenACC 2.6 (multicore CPUs & GPUs)
- AVX-512
- PGI compiler assisted software testing
- Debugging tools to investigate differences between CPU & accelerators
- Profiling tools

The PGI license is made available through the HPC.NRW Kompetenznetzwerk.


### Setup
The PGI license is currently only available on our **fugg1** node.
Connect to it from within the university network via

```bash
ssh fugg1.pleiades.uni-wuppertal.de
```

Additionally you need to setup your user environment:

```bash
export PGI="/beegfs/pgi"
export PATH="${PGI}/linux86-64/20.4/bin":$PATH
export MANPATH=$MANPATH:"${PGI}/linux86-64/20.4/man"
export LM_LICENSE_FILE=$LM_LICENSE_FILE:"${PGI}/license.dat"
```

This can also be done via

```bash
source /beegfs/pgi/scripts/setup.sh
```

Consider putting this into your bashrc, if you have to call it regularly!


### Small Fortran Test
To check if the compilers work, lets create a small Fortran program `hello.f90`:

```fortran
print *, "Hello world"
end
```

Now compile and execute it via:

```bash
pgfortran -o hello hello.f90
./hello
```

### Additional Resources
Get an overview of possible command line options via the man pages, e.g. `man pgfortran` or through the `-help` flag. (not `-h` or `--help`!)

There is a lot of information on the PGI page as well:

- [Dokumentation](https://www.pgroup.com/resources/docs/20.1/x86/index.htm)
- [User Guide](https://www.pgroup.com/resources/docs/20.1/x86/pgi-user-guide/index.htm)
- [Profiler User Guide](https://www.pgroup.com/resources/docs/20.1/x86/profiler-users-guide/index.htm)
- [OpenACC User Guide](https://www.pgroup.com/resources/docs/20.1/x86/openacc-gs/index.htm)
- [Tutorials](https://www.pgroup.com/resources/tutorials.htm)
- [Porting & Tuning Guides](https://www.pgroup.com/resources/tips.htm) (requires account and login)
