---
title: "Jupyter: JupyterHub on PLEIADES"
---

## Jupyter: JupyterHub on PLEIADES

PLEIADES provides users with easy access to a JupyterLab graphical user interface (GUI) that comes with preset resources. This is possible through a JupyterHub virtual machine that can only be reached from within the university's network. Depending on the user's location, a VPN may be necessary to access it. From the JupyterHub interface, users can automatically spawn JupyterLab servers and connect to them. The procedure for spawning is technically identical to batching any other job on PLEIADES. Therefore, depending on the current cluster load, it may take some time until the JupyterLab server job leaves the pending status (*PD*). Even after the job is already running (status *R*), it may take a few seconds for JupyterLab to establish a connection with JupyterHub because some modules need to be loaded from BeeGFS.

### JupyterHub: Available resources

Users cannot adjust computational resources individually. Instead, the Scientific Computing Center PLEIADES provides multiple profiles with different resources that users can choose from. These profiles have been designed to make it easy for users to interact with their code for the purpose of performance analysis and quick evaluation. If users require a more customized resource setup, we also [provide a guide]({{ site.baseurl }}{% link Jupyter/jupyter-nb_on_pleiades.md %}) to interactive Jupyter-notebook sessions with individually set resource requirements.  
  
If you are using Python packages in your simulations, you need to make sure, that they are also available in your JuyterLab server sessions. If you were using your own Python installation, which is not *Python 3.9.5*, you will need to install the packages again with *Python 3.9.5*. The easiest way to do so is to start a JupyterLab server through JupyterHub and to open a bash terminal inside (*File-&gt;New-&gt;Terminal*). This ensures that the right `pip` installation will be used for `python pip install --user <PACKAGE>`.



### JupyterHub: Access and usage

To access the JupyterHub VM, users can [follow this link](https://jupyterhub.pleiades.uni-wuppertal.de/).
  
Any important information about the cluster, such as planned maintenance, will be displayed here as well. Users will be asked to enter their credentials, which are the same as their PLEIADES account. Once users have signed in and assuming they have no JupyterLab server instance running, they will be directed to the profile spawn menu. Otherwise, they will be directed to their running JupyterLab server.

[![Dropdown menu for the profile selection](../assets/img/jupyterhub/jh_profileSelection.png)](../assets/img/jupyterhub/jh_profileSelection.png "Dropdown menu for the profile selection")

The available resources in the provided profiles are designed to give users the possibility of interactively evaluating their code and analyzing its multiprocessor capabilities. The following limitations apply to the JupyterLab servers initialized through JupyterHub:

* Runtime of JupyterLab servers is limited to 1 day.
* If jobs spend more than 8 hours with the pending status *PD*, they will be automatically cancelled. This is to prevent unattended JupyterLab servers from spawning and occupying valuable resources.
* Profiles with multiple CPUs or GPUs still have to spawn jobs on one single node. This means that a profile offering 4 CPUs will batch a job that waits until at least 4 CPUs are free on one node. Users should keep this in mind when available resources are limited.

Once a profile has been selected, users can batch the JupyterLab server job by clicking on the "Start" button. This will automatically redirect users to the spawning screen, where they will remain until the job is running and connects to JupyterHub. Depending on the current cluster load, this can take some time. The graphic below shows a spawning screen with an open Event Log (opened by clicking on it).

[![Spawning screen with open event log](../assets/img/jupyterhub/jh_pending.png)](assets/img/jupyterhub/jh_pending.png "Spawning screen with open event log")

Here users can see, that

1. A server has been requested, i.e. a job has been submitted.
2. The job has been pending in the queue (status *PD*).
3. The job has started and JupyterHub is now awaiting a connection to the JupyterLab server.

Please note that the cluster job running (point 3) is usually not identical to the JupyterLab server being available. The batched job contains other instructions besides starting the actual JupyterLab server, and therefore it might take some time before a running job actually connects to JupyterHub.
  
  
Once the connection to the JupyterLab server is established, the JupyterLab UI will be displayed as shown below.
  
[![GUI of JupyterLab](../assets/img/jupyterhub/jh_interface.png)](assets/img/jupyterhub/jh_interface.png "GUI of JupyterLab")
  
A comprehensive guide on how to use JupyterLab and its various features can be found [in the official documentation](https://jupyterlab.readthedocs.io/en/stable/user/interface.html).
  
It is crucial to properly terminate your JupyterLab session as closing your browser window or logging out will not close the JupyterLab server. To end your session correctly, first, navigate to the Hub Control Panel by clicking on *File-&gt;Hub Control Panel* located in the top left corner of the interface. This will open the Hub Control Panel in a new window as shown below:
  
[![Hub Control Panel](../assets/img/jupyterhub/jh_hubControl.png)](assets/img/jupyterhub/jh_hubControl.png "Hub Control Panel")
  
Clicking on *My Server* or on the logo of the BUW will bring you back to your running JupyterLab server, whereas clicking on *Stop my Server* will terminate the job. Depending on the cluster load, this might take some time, however, after clicking on *Stop my Server* you are free to close all windows.  
If you are unable to terminate your server for any reason, you can always cancel the job manually by entering `scancel <JOBID>` in the terminal on the cluster, just like any other job on SLURM.

### JupyterHub: Virtual environments with pip for custom IPython kernels

$\textcolor{red}{\text{CURRENTLY ONLY KERNELS BASED ON PYHON 3.9.5 ARE SUPPORTED}}$ 

Virtual environments provide a way to create isolated and self-contained Python environments with their own set of packages and dependencies, making it easier to manage different projects without interference. In this section, we will explore the steps required to create a virtual environment using pip and install the necessary packages for running JupyterLab with IPython kernels. These kernels can then be selected from the JupyterLab interface and will provide the associated virtual environment from within your JupyterLab session.

As long as you want to have a virtual environment, which uses Python 3.9.5 (the Python version of JupyterHub), you can do all steps from within a JupyterLab session's terminal. However, we highly suggest to follow our instructions while on one of the log in nodes or on a worker node. Usually, the latter should be preferred, as not to overload the log in nodes.

Before you start, you need to make sure, that you have a Python version loaded inside your current environment. If you do not have a Python version of your own, you can simply load one of the `module`s provided by the Scientific Computing Center PLEIADES. You can make sure you are using the desired Python binaries through `which python` and the version through `python --version`. Once ready, do the following to customize your virtual environment:

1. Generate the virtual environment: `python -m venv <name_of_virtual_env>`
2. Activate the virtual environment: `source /pathToVirtualEnvironment/bin/activate`
3. Install the *ipykernel* package into the virtual environment: `python -m pip install ipykernel`
4. Install any other required packages through pip: `python -m pip install <package1> <package2> ... <packageN>` 
5. Use the *ipykernel* package to install a new IPython kernel for JupyterLab servers: `python -m ipykernel install --user --name 'NameOfKernelALPHANUMERICAL' --display-name "Name of kernel displayed in Jupyter"`
6. To not further mess with your kernel you should deactivate the corresponding virtual environment: `de´activate`

After the activation of a virtual environment in **step 2**, you should see the name of the environment next to your usual prompt of the CLI, e.g. `(name_of_virtual_env) -bash-4.2$`. Likewise, it should disappear after **step 6**. Once a kernel has been created in **step 5**, you should almost immediately have it available in any (even already running) JupyterLab server. More importantly, you can continue modifying your custom kernels simply by sourcing its virtual environment (**step 2**) and then repeating **step 4** as much as you need.  
The command from point 5 installs your kernel into your local directory at `~/.local/share/jupyter/kernels`. Jupyter automatically checks this directory and makes these kernels available to the user through the JupyterLab interface.



### JupyterHub: Virtual environments with Conda for custom IPython kernels

$\textcolor{red}{\text{CURRENTLY ONLY KERNELS BASED ON PYHON 3.9.5 ARE SUPPORTED}}$

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


  
### JupyterHub: FAQ
* **What versions are you using?**  
We are using *JupyterHub 3.1.1* and *JupyterLab 3.2.8*.
* **I have mutliple jobs running on the cluster. How do I know which one is the JupyterLab server?**  
All JuypterLab server jobs get the same name, *spawner-jupyterhub*, although only a few characters might be visible when entering `squeue -u <USERNAME>`.
* **I want to check my JuypterLab logfiles. Where are they located?**  
The logfiles belonging to the JupyterLab job (not just for JupyterLab itself) are always written to the users home directory and have the following naming convention: *jupyterhub_slurmspawner_&lt;JOBID&gt;.log*
* **I would love to have more resources available for my JupyterLab session. Is this possible?**  
Yes, you can manually configure and submit a JupyterLab server without using JupyterHub. Instructions on how to do this are provided [here]({{ site.baseurl }}{% link Jupyter/jupyter-nb_on_pleiades.md %}). 
* **Why can't I find my Python packages?**  
Please see the section [Available Resources](#jupyterhub:-available-resources) to ensure you have installed the packages into the right Python directory.
















