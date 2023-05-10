---
title: "Creating kernels for Julia and R"
---

## Jupyter: Creating kernels for Julia and R

One of the key features of Jupyter is its support for multiple programming languages, including Julia and R. However, to use Jupyter with these languages, you need to install and configure language-specific kernels. In this section, we will explain how to create kernels for Julia and R based on PLEIADES' `module` software stack.  


### Jupyter: Creating Julia kernels

Creating a Julia kernel, which is usable on your JupyterLab servers is finished in a few steps:

1. Load a Julia `module`:  
`module load 2021a Julia/1.7.3-linux-x86_64`
2. Start Julia be entering `julia`
3.Enter the following two lines in Julia:  
```
using Pkg
Pkg.add("IJulia")
```
This will install the IJulia package and create the Julia kernel in `~/.local/share/jupyter/kernels/`. This path is used by your [IPython kernels](./jupyter-kernels.md), too. Further, while it is possible to install Julia packages from your Julia notebook in Jupyter, we highly suggest to do this on one of the login nodes or worker nodes, in order to prevent any conflicts with modules loaded by Jupyter.  
Please also take a look at [IJulia's documentation](https://julialang.github.io/IJulia.jl/stable/manual/installation/).  

