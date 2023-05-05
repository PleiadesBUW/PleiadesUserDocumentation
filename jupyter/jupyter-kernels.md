---
title: "Creating custom kernels with IPython"
---

## Jupyter: Creating custom kernels with IPython

Virtual environments provide a way to create isolated and self-contained Python environments with their own set of packages and dependencies, making it easier to manage different projects without interference. In this section, we will explore the steps required to create a virtual environment using *pip* and *venv* or *Conda* and install the necessary packages for running JupyterLab with IPython kernels. These kernels can then be selected from the JupyterLab interface and will provide the associated virtual environment from within your JupyterLab session.
  
As long as you want to have a virtual environment, which uses Python 3.9.5 (the Python version of JupyterHub), you can do all steps from within a JupyterLab session's terminal. However, we highly suggest to follow our instructions while on one of the log in nodes or on a worker node. Usually, the latter should be preferred, as not to overload the log in nodes. Further, if no local Python installation is used for the kernel, the `module load` command should load a Python version with the affix *-bare*. This ensures, that virtual environments will be void of Python modules/packages upon creation (in particular the environment variable *PYTHONPATH* will remain empty).
  
We first begin with instructions regarding IPython kernels based on Python 3.9.5. **Due to the modular structure of PLEIADES' software stack it is not trivial to utilize new IPython kernels for other Python versions than 3.9.5.** Nevertheless, at the end of this section we provide instructions on how to generate IPython kernels for arbitray Python versions (supported by IPython) through *pip* and *venv* only. It requires some additional modifications in the *kernel.json* file and correct Python paths. We do not provide support for such custom kernels and therefore user caution is advised. 

### JupyterHub: Virtual environments with pip for custom IPython kernels (with Python 3.9.5)

Before you start, you need to make sure, that you have Python version 3.9.5 loaded inside your current environment. If you do not have a Python 3.9.5 of your own, you can simply load it through one of the `module`s provided by the Scientific Computing Center PLEIADES. You can make sure you are using the desired Python binaries through `which python` and the version through `python --version`. Once ready, do the following to customize your virtual environment:

1. Generate the virtual environment: `python -m venv <name_of_virtual_env>`
2. Activate the virtual environment: `source /pathToVirtualEnvironment/bin/activate`
3. Install the *ipykernel* package into the virtual environment: `python -m pip install ipykernel`
4. Install any other required packages through *pip*: `python -m pip install <package1> <package2> ... <packageN>` 
5. Use the *ipykernel* package to install a new IPython kernel for JupyterLab servers:   
`python -m ipykernel install --user --name 'NameOfKernelALPHANUMERICAL' --display-name "Name of kernel displayed in Jupyter"`
6. To not further mess with your kernel you should deactivate the corresponding virtual environment: `deactivate`

After the activation of a virtual environment in **step 2**, you should see the name of the environment next to your usual prompt of the CLI, e.g. `(name_of_virtual_env) -bash-4.2$`. Likewise, the name of the environment should disappear after **step 6**. Once a kernel has been created in **step 5**, you should almost immediately have it available in any (even already running) JupyterLab server. More importantly, you can continue modifying your custom kernel simply by sourcing its virtual environment (**step 2**) and then repeating **step 4** as much as you need to.  
The command from **step 5** installs your kernel into your local directory at `~/.local/share/jupyter/kernels`. Jupyter automatically checks this directory and makes these kernels available to the user through the JupyterLab interface.



### JupyterHub: Virtual environments with Conda for custom IPython kernels (with Python 3.9.5)

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




