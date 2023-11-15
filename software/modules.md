---
title: "Modules"
layout: default
parent: Software on PLEIADES
nav_order: 2
---

## Software: Modules
We use [LMod](https://lmod.readthedocs.io/) environment modules to provide software installations to our users.
Please have a look at the [LMod user guide](https://lmod.readthedocs.io/en/latest/010_user.html).

### Finding Modules
After login, you can use the `module` command to find and load software:
```
$ module --help
Usage: module [options] sub-command [args ...]

Options:
  -h -? -H --help                   This help message
.
.
.
```

For example, if you need a recent version of GCC and CMake and want to see available versions, you can execute:
```
$ module spider GCC CMake
-------------------------------------------------------------------------------
  GCC:
-------------------------------------------------------------------------------
    Description:
      The GNU Compiler Collection includes front ends for C, C++, Objective-C, Fortran, Java, and Ada, as well as libraries for these languages (libstdc++, libgcj,...).

     Versions:
        GCC/4.9.3-2.25
        GCC/8.3.0
        GCC/9.3.0
        GCC/10.2.0
        GCC/10.3.0
     Other possible modules matches:
        GCCcore

-------------------------------------------------------------------------------
  CMake:
-------------------------------------------------------------------------------
    Description:
      CMake, the cross-platform, open-source build system. CMake is a family of tools designed to build, test and package software.

     Versions:
        CMake/3.15.3
        CMake/3.16.4
        CMake/3.18.4
        CMake/3.20.1

-------------------------------------------------------------------------------
  To find other possible module matches execute:

      $ module -r spider '.*GCC.*'

-------------------------------------------------------------------------------
  For detailed information about a specific "CMake" package (including how to load the modules) use the module's full name.
  Note that names that have a trailing (E) are extensions provided by other modules.
  For example:

     $ module spider CMake/3.20.1
-------------------------------------------------------------------------------
```

Use the specific versions of GCC and CMake, to see details on how to load these modules:
```
$ module spider GCC/10.3.0 CMake/3.20.1

-------------------------------------------------------------------------------
  GCC: GCC/10.3.0
-------------------------------------------------------------------------------
    Description:
      The GNU Compiler Collection includes front ends for C, C++, Objective-C, Fortran, Java, and Ada, as well as libraries for these languages (libstdc++, libgcj,...).


    You will need to load all module(s) on any one of the lines below before the "GCC/10.3.0" module is available to load.

      2021a
      2021a-norpath

    Help:
      Description
      ===========
      The GNU Compiler Collection includes front ends for C, C++, Objective-C, Fortran, Java, and Ada,
       as well as libraries for these languages (libstdc++, libgcj,...).


      More information
      ================
       - Homepage: https://gcc.gnu.org/



-------------------------------------------------------------------------------
  CMake: CMake/3.20.1
-------------------------------------------------------------------------------
    Description:
      CMake, the cross-platform, open-source build system. CMake is a family of tools designed to build, test and package software.


    You will need to load all module(s) on any one of the lines below before the "CMake/3.20.1" module is available to load.

      2021a  GCCcore/10.3.0
      2021a-norpath  GCCcore/10.3.0

    Help:
      Description
      ===========
      CMake, the cross-platform, open-source build system.  CMake is a family of
       tools designed to build, test and package software.


      More information
      ================
       - Homepage: https://www.cmake.org
```

To finally load the software you can call `module load 2021a GCC/10.3.0 CMake/3.20.1`:
```
$ gcc --version
gcc (GCC) 10.3.0
Copyright (C) 2020 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

$ which gcc
/beegfs/Tools/easybuild/stacks/rome/2021a/software/GCCcore/10.3.0/bin/gcc

$ which cmake
/beegfs/Tools/easybuild/stacks/rome/2021a/software/CMake/3.20.1-GCCcore-10.3.0/bin/cmake
```

You would use such a `module load` command, whenever you prepare your environment for interactive work, as well as in batch job scripts.


### Background Information: Hierarchical Naming Scheme
The module visibility is following a hierarchical naming scheme.
It means that software only becomes visible to the `module available` command, after it's dependencies are loaded.
`2021a` represents a "meta module", acting as an entry point into a given version of the software stack.
A new software stack is released about every 6 months and older stacks will be phased out over time.
To ensure reproducibility, you can still access old software stacks by calling `module use /beegfs/Tools/easybuild/old-meta-module/`, but we recommend to use the most recent versions whenever possible.

The hierarchical naming scheme can make it difficult to discover available software.
If you look for something specific, just use `module spider <software>` to get an overview and `module spider <software>/<version>` to learn what dependencies have to be loaded.
You can also look into `/beegfs/Tools/easybuild/stacks/rome/2021a/software/` to get a general idea of the available software (as of 2021-11-18):
```bash
$ ls /beegfs/Tools/easybuild/stacks/rome/2021a/software/
Anaconda3          flatbuffers            h5py             libiconv       numactl          SciPy-bundle
ATK                flatbuffers-python     HarfBuzz         libjpeg-turbo  NVHPC            Score-P
at-spi2-atk        flex                   HDF5             libpciaccess   OPARI2           SCOTCH
at-spi2-core       FlexiBLAS              help2man         libpng         OpenBLAS         SDL2
Autoconf           fmt                    hwloc            libreadline    OpenFOAM         SIONlib
Automake           fontconfig             hypothesis       LibTIFF        OpenMPI          snappy
Autotools          foss                   ICU              libtool        OpenSSL          spdlog
Bazel              freeglut               iimpi            libunwind      OTF2             SQLite
binutils           freetype               imkl             libxml2        Pango            Szip
Bison              FriBidi                impi             libyaml        PAPI             Tcl
Boost              GCC                    intel            likwid         Paraver          TensorFlow
bzip2              GCCcore                intel-compilers  LLVM           ParaView         Theano
cairo              GDB                    intltool         LMDB           PCRE             typing-extensions
CFITSIO            Gdk-Pixbuf             ISL              Lua            PCRE2            UCX
CGAL               gettext                JasPer           lz4            PDT              UnZip
Clang              giflib                 Java             M4             Perl             util-linux
CMake              git                    jbigkit          makeinfo       pixman           Valgrind
CubeGUI            GL2PS                  JsonCpp          Mako           pkgconfig        wxWidgets
CubeLib            glew                   JUBE             MCFM           pkg-config       X11
CubeWriter         GLib                   Keras            Mesa           PMIx             x264
CUDA               GMP                    LAME             Meson          protobuf         x265
cURL               gnuplot                libarchive       METIS          protobuf-python  xorg-macros
DB                 GObject-Introspection  libcerf          MPC            pybind11         XZ
DBus               gompi                  libdrm           MPFR           Python           Yasm
double-conversion  gperf                  libepoxy         NASM           PyYAML           Z3
Doxygen            groff                  libevent         ncurses        Qt5              Zip
Eigen              GSL                    libfabric        netCDF         re2c             zlib
elfutils           GST-plugins-base       libffi           Ninja          ROOT             zstd
expat              GStreamer              libgd            NSPR           Rust
FFmpeg             GTK3                   libGLU           NSS            ScaLAPACK
FFTW               gzip                   libglvnd         nsync          Scalasca
```

### Background Information: Module Build
We build the software stacks with [EasyBuild](https://docs.easybuild.io/en/latest/).
All modules have been built on our [wn21](../hardware) nodes and therefore optimized for the AMD Rome architecture.

If you are missing a software, please check if it is available in [EasyBuilds list of supported software](https://docs.easybuild.io/en/latest/version-specific/Supported_software.html#list-software).
In this case it might be trivial for us to provide it as well.
Otherwise, we hope you understand that building unsupported software may require some time or might not be feasible for us.
If you have made your own modules, e.g. in `/beegfs/<user>/modules`, you can add the path via `module use /path/to/my/modules`.


### Troubleshooting
Occasionally you are in a situation where you know a new module just has been made available, but the `module` command claims it is not.
In this case you can try `module --ignore-cache spider`, since available module locations might be cached in your user directory.

If you load many modules, you might experience an increased latency in your interactive shell commands.
This is caused by a growing `LD_LIBRARY_PATH` environment variable, when modules from the `2021a` (and any `-norpath`) stack are loaded.
The growing `LD_LIBRARY_PATH` is causing frequent search operations on our `/beegfs`, which can be slow if many file system operations occur in parallel, e.g. through other users jobs.

We are working on mitigations of this issue and as a workaround you can try to use the software from the `2021a-rpath` stack instead.
Here, the whole software stack is built with `RPATH`, i.e. lookup paths of library dependencies are compiled into all binaries and the lookup through `LD_LIBRARY_PATH` is not necessary anymore.
There are a couple of drawbacks though:
  - The `2021a-rpath` stack is not necessarily suited to build your own software, e.g. by loading GCC and CMake. In this case you would have to consider using RPATH in your build as well. For simplicity, try to use the `-rpath` stack only if you intend to use the application, but not build further software against it.
  - The `2021a`(`-norpath`) and `2021a-rpath` stack might be out of sync. Please tell us if you need a certain module that is available in one, but no the other.


## Alternative to Modules: LCG
The SFT project at CERN provides multiple LCG releases that bundle many useful software packages. This also contains different versions of the gcc or clang compilers. If CVMFS is available, you can set up a specific release via

```bash
source /cvmfs/sft.cern.ch/lcg/views/LCG_99/x86_64-centos7-gcc8-opt/setup.sh
```

CVMFS is available on all login and worker nodes of PLEIADES.

If you are only interested in a single tool, e.g. gcc, you can source the corresponding setup.sh, e.g. in

```bash
/cvmfs/sft.cern.ch/lcg/releases/gcc/8.1.0/x86_64-centos7/setup.sh
```

Please check if the version of the operating system (here centos7) matches the architecture in that path. For more info refer to the [LCG info web page](https://ep-dep-sft.web.cern.ch/document/lcg-releases) or the README files in /cvmfs/sft.cern.ch/lcg/
