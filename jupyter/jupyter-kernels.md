---
title: "Creating custom kernels with IPython"
layout: default
parent: Jupyter
nav_order: 3
---

## Jupyter: Creating custom kernels with IPython

Virtual environments provide a way to create isolated and self-contained Python environments with their own set of packages and dependencies, making it easier to manage different projects without interference. In this section, we will explore the steps required to create a virtual environment using *pip* and *venv* or *Conda* and install the necessary packages for running JupyterLab with IPython kernels. These kernels can then be selected from the JupyterLab interface and will provide the associated virtual environment from within your JupyterLab session.
  
As long as you want to have a virtual environment, which uses Python 3.10.4 (the Python version of JupyterHub), you can do all steps from within a JupyterLab session's terminal. However, we highly suggest to follow our instructions while on one of the login nodes or on a worker node. Usually, the latter should be preferred, as not to overload the login nodes. Further, if no local Python installation is used for the kernel, the `module load` command should load a Python version with the affix *-bare*. This ensures, that virtual environments will be void of Python modules/packages upon creation (in particular the environment variable *PYTHONPATH* will remain empty).
  
We first begin with instructions regarding IPython kernels based on Python 3.10.4. **With the modular structure of PLEIADES' software stack it is not trivial to utilize new IPython kernels for other Python versions than 3.10.4.** Nevertheless, at the end of this section we provide instructions on how to generate IPython kernels for arbitray Python versions (supported by IPython) through *pip* and *venv* only. They require some additional modifications in the *kernel.json* file and correct Python paths. We do not provide support for such custom kernels and therefore user caution is advised. 

### JupyterHub: Virtual environments with pip for custom IPython kernels (with Python 3.10.4)

Before you start, you need to make sure, that you have Python version 3.10.4 loaded inside your current environment. If you do not have a Python 3.10.4 of your own, you can simply load it through one of the centrally provided`module`.
You can make sure you are using the desired Python binaries through `which python` and the version through `python --version`.
Once ready, do the following to customize your virtual environment:

1. Generate the virtual environment: `python -m venv <name_of_virtual_env>`
2. Activate the virtual environment: `source /pathToVirtualEnvironment/bin/activate`
3. Install the *ipykernel* package into the virtual environment: `python -m pip install ipykernel`
4. Install any other required packages through *pip*: `python -m pip install <package1> <package2> ... <packageN>` 
5. Use the *ipykernel* package to install a new IPython kernel for JupyterLab servers:   
`python -m ipykernel install --user --name 'NameOfKernelALPHANUMERICAL' --display-name "Name of kernel displayed in Jupyter"`
6. To not further mess with your kernel you should deactivate the corresponding virtual environment: `deactivate`

After the activation of a virtual environment in **step 2**, you should see the name of the environment next to your usual prompt of the CLI, e.g. `(name_of_virtual_env) -bash-4.2$`. Likewise, the name of the environment should disappear after **step 6**. Once a kernel has been created in **step 5**, you should almost immediately have it available in any (even already running) JupyterLab server. More importantly, you can continue modifying your custom kernel simply by sourcing its virtual environment (**step 2**) and then repeating **step 4** as much as you need to.  
The command from **step 5** installs your kernel into your local directory at `~/.local/share/jupyter/kernels`. Jupyter automatically checks this directory and makes these kernels available to the user through the JupyterLab interface.



### JupyterHub: Virtual environments with Conda for custom IPython kernels (with Python 3.10.4)

If you prefer to manage your packages and virtual environments with Conda, you can do this, aswell. To activate the basic Conda environment *(base)* do the following:

1. Open a terminal in your JupyterLab server: *File-&gt;New-&gt;Terminal*
2. In the terminal enter `conda activate`. Afterwards you should see a *(base)* before the prompt indicating that you are in the *base* environment.

You can deactivate this environment, like any conda environment, by typing `conda deactivate`. To disable the automatic activation of the *base* environment enter `conda config --set auto_activate_base false`.

Often users are not interested in using only the *base* environment in order to prevent conflicts between differrent packages. In order to configure your own environment with packages and use it within any of your JupyterLab servers, follow these steps:

1. Create your conda environment: `conda create --name <name_of_virtual_env>`
2. Activate it (from anywhere): `conda activate <name_of_virtual_env>`
3. Install any packages you like **through conda**
4. Install the *ipykernel* package in the chosen conda virtual environment: `conda install ipykernel`
5. Enable the kernel: `python -m ipykernel install --user --name 'Name-of-my-venv' --display-name "Displayed name of my venv"` 
6. Start a new Jupyter notebook: *File-&gt;New-&gt;Notebook*
7. Select your kernel from the dropdown menu according to your option `--display-name` from point 5
  
If you prefer, you can also install the kernels while directly logged in to PLEIADES instead of the JupyterHub log in.

### JupyterHub: Virtual environments with pip for custom IPython kernels (other Python versions)

Utilizing kernels with a Python version other than 3.10.4 will result in the kernel not properly loading displayed in the JupyterLab interface as *disconnected*. This originates in the modules loaded for JupyterLab server start-up, which cause conflicts with other python libraries.  
To generate an IPython kernel for an arbitrary Python version you can initially proceed as you would for a custom IPython kernel with Python 3.10.4:

1. Generate the virtual environment: `python -m venv <name_of_virtual_env>`
2. Activate the virtual environment: `source /pathToVirtualEnvironment/bin/activate`
3. Install the *ipykernel* package into the virtual environment: `python -m pip install ipykernel`
4. Install any other required packages through *pip*: `python -m pip install <package1> <package2> ... <packageN>` 
5. Use the *ipykernel* package to install a new IPython kernel for JupyterLab servers:   
`python -m ipykernel install --user --name 'NameOfKernelALPHANUMERICAL' --display-name "Name of kernel displayed in Jupyter"`
6. To not further mess with your kernel you should deactivate the corresponding virtual environment: `deactivate`

Comments on some of these steps are provided further above in the [corresponding section](#jupyterhub-virtual-environments-with-conda-for-custom-ipython-kernels-with-python-3104).  
Afterwards, follow these steps (we will use an IPython kernel based on Python 3.9.5 as an example):

1. Go to the location of your kernel (usually something like `~/.local/share/jupyter/kernels/NameOfKernelALPHANUMERICAL`)
2. Change the argument vector (`argv`) in `kernel.json` from something like this:  
```
{
 "argv": [
  "/pathToVirtualEnvironment/bin/python",
  "-m",
  "ipykernel_launcher",
  "-f",
  "{connection_file}"
 ],
 "display_name": "Name of kernel displayed in Jupyter",
 "language": "python",
 "metadata": {
  "debugger": true
 }
}
```
to something like this:  
```
{
 "argv": [
  "~/.local/share/jupyter/kernels/NameOfKernelALPHANUMERICAL/initKernel.sh",
  "-f",
  "{connection_file}"
 ], 
"display_name": "Name of kernel displayed in Jupyter",
 "language": "python",
 "metadata": {
  "debugger": true
 }
}
```
Note, that you only change the content of `argv` and nothing else in that file. The argument vector will now point to a file `initKernel.sh`, which will be executed at the start up of the kernel.
3. Create the file `initKernel.sh` in `~/.local/share/jupyter/kernels/NameOfKernelALPHANUMERICAL/` based on the following content:  
```bash
#!/usr/bin/env bash
# Get rid of any modules loaded by JupyterHub / JupyterLab
module purge
# Load the same modules which you have loaded
# for the creation of the virtual environment.
# If you are using your own insallation of Python
# without any modules then you have to leave 
# the next line empty.
module load 2022a  GCCcore/11.3.0 Python/3.9.5-bare
# This is CRITICAL and should ALWAYS be at the end of your script
exec /pathToVirtualEnvironment/bin/python -m ipykernel $@
```  
  
Keep in mind that the additional loading of modules in `initKernel.sh` might take some time. Keep an eye out on the kernel status, and once it states *idle*, you can start working.
