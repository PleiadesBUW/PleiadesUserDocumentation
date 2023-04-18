---
title: "Jupyter: JupyterHub on PLEIADES"
---

## Jupyter: JupyterHub on PLEIADES

PLEIADES provides easy access to a JupyterLab GUI with pre-selected resources for its users. This is achieved through a JupyterHub VM, which is only reachable from within the university's network, so depending on your location a VPN might be necessary. From the JupyterHub interface users can spawn JupyterLab servers and automatically connect to them. This spawning procedure is automated, but technically identical to batching any other job on PLEIADES. This means that depending on current cluster load it might take some time until your JupyterLab server job leaves the pending status *PD*. Even after the job is running (status *R*) it might take a few seconds for JupyterLab to establish connection with JupyterHub, because some modules need to be loaded from BeeGFS.

### JupyterHub: Available resources

Computational resources cannot be individually adjusted by the user. Instead multiple profiles (with different resources) are provided by the PLEIADES scientific computing center from which the user can select the most appropriate one. The profiles have been designed, so users can easily interact with their code for the purpose of performance analysis and quick evaluation. If a more customized resource setup is desired, we also provide a guide to interactive Jupyter-notebook sessions with individually set resource requirements.


### JupyterHub: Access and Usage

To sign in into the JupyterHub VM simply follow this link:
  
(https://jupyterhub.pleiades.uni-wuppertal.de/).
  
Any important information regarding the cluster (for example planned maintenance) will be displayed here, aswell.
You will be asked to enter your credentials, which are identical to your PLEIADES account. Once signed in and assuming you have no instance of a JupyterLab server running, you will be directed to the profile spawn menu.

[![Dropdown menu for the profile selection](assets/img/jupyterhub/jh_profileSelection.jpg) "Dropdown menu for the profile selection"](assets/img/jupyterhub/jh_profileSelection.jpg)

The available resources in the provided profiles are designed to give users the possibility of interactively evaluating and analyzing the multiprocessor capabilities of their code. Further, the following limitations apply to the JupoyterLab servers initialized through JupyterHub:

* Runtime of JupyterLab servers is limited to 1 day.
* If jobs spend more than 8 hours with the pending status *PD* they will be automatically cancelled. This is to prevent unattended JupyterLab servers from spawning and occupying valuable resources.
* Profiles with multiple CPUs or GPUs still have to spawn jobs on one single node. This means that a profile offering 4 CPUs will batch a job that waits until at least 4 CPUs are free on one node. Keep this in mind when available resources are limited.

Once a profile has been selected, the JupyterLab server job can be batched by clicking on the *Start*-Button. This will automatically redirect you to the spawning screen, where you will remain until the job is running and connects to JupyterHub. Depending on the current cluster load this can take some time. The graphic below shows a spawning screen with an open *Event Log* (opened by clicking on it).

[IMAGE PENDING]

Here we can see, that

1. A server has been requested, i.e. a job has been submitted.
2. The job has been pending in the queue (Status *PD*).
3. The job has started and JupyterHub is now awaiting a connection to the JupyterLab server.

Please keep in mind, that the cluster job running (point 3) is usually not identical to the JuypterLab server beeing available. The batched job contains other instructions besides starting the actual JupyterLab server and therefore it might take some time before a running job actually connects to JupyterHub.
  
  
When the connection to the JuypterLab server is ready you will see the JupyterLab GUI shown below.
  
[IMAGE GUI]

A detailed description of all functionalities and how to work with JupyerLab can be found in the official documentation:
  
(https://jupyterlab.readthedocs.io/en/stable/user/interface.html)
  
We would only like to emphasize how to correctly end a JupyterLab session, as closing your browser window or logging out does not close the JupyterLab server. First, you will have to go to the Hub Control Panel by clicking in the top left corner of the interface on *File->Hub Control Panel*. This opens the Hub Control Panel in a new window:

[IMAGE HUB]

Clicking on *My Server* or on the Logo of the BUW will bring you back to your running JupyterLab server, whereas clicking on *Stop my Server* will end the job. Depending on the cluster load this might take some time, however, after clicking on *Stop my Server* you are free to close all windows.  
Should you not able to end your server for any reason, you can always cancle the job, like any other job on SLURM, manually by entering `scancel <JOBID>` in the terminal on the cluster.













### Safely Accessing Jupyter Notebooks on PLEIADES
The safest way to initiate an interactive Jupyter Notebooks session is by using sockets for tunneling the notebook data.  This process consists of the following three steps:

1. Submit a Jupyter Notebook job to a worker node with the resources your job(s) will require.
2. Use `ssh` to tunnel from your local machine to the worker node from step 1, using a login node as a jump host.
3. Access the notebook through your preferred browser.

We will now explain these steps in more detail, along with the necessary inputs.

#### Step 1: Submitting a Jupyter Notebook Job
To initiate your Jupyter Notebook, you will need to submit a job that runs the following command:

```bash
srun -n1 jupyter-notebook --no-browser --no-mathjax --sock /beegfs/$USER/jupyter_wn.socket
```

This command starts a Jupyter session and directs it to the socket `jupyter_wn.socket` in your `$HOME` directory. This socket will later be used to `ssh` tunnel to the Jupyter notebook.  
  
When writing your batch script, keep in mind that the provided resource requirements should be based on the calculations you want to perform and not on the notebook itself.  
  
Once you have an idea of the resources you will need (e.g. CPUs, GPUs, memory), create a BATCH file as usual.  
Here is an example:

```bash
#!/bin/sh
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=1
#SBATCH --time=01:00:00
#SBATCH --mem-per-cpu=128
#SBATCH --job-name=jupyter-nb
#SBATCH --output=jupyter-%j.log
#SBATCH --error=jupyter-%j.err

# Some initial setup
module purge
# This module load instruction is where the Jupyter client comes from
module load 2021a  GCCcore/10.3.0  JupyterLab/3.2.8
# Here you can load any additional modules you might need, e.g. for your simulations or for running R notebooks
# module load my other modules

# optional: if you have a Conda env, activate it here:
#conda activate testenv

# Start the notebook and send data through socket jupyter_wn.socket in $HOME
srun -n1 jupyter-notebook --no-browser --no-mathjax --sock /beegfs/$USER/jupyter_wn.socket
```
    
Feel free to adjust resource requirements to your needs. 

Once the job has been submitted, you will need to wait until the Jupyter notebook is actually running. You can check the job's status by entering `squeue -u $USER | grep jupyter-nb`, which will also provide you with the worker node on which the notebook is running (required for **step 2**). To confirm that all modules have been loaded and Jupyter is being forwarded to the socket, check the corresponding *.err*-file. When it contains a section with  
```
http://localhost:8888/?token=longTokenString
```
you are ready for the next step.


#### Step 2: Tunneling to the Worker Node
Worker nodes cannot be accessed from outside. This means, you cannot directly tunnel through socket `jupyter_wn.socket` from your local machine. Instead, you have to use one of the login nodes (*fugg1* or *fugg2*) as a jump host:

```
ssh -L 8888:/beegfs/<username>/jupyter_wn.socket -J fugg1.pleiades.uni-wuppertal.de <username>@<worker-node>.pleiades.uni-wuppertal.de -N
```

You can change `8888` to a different local port if you need to. The port number is relevant in the next step. \
\
Leave that terminal window open to sustain the connection to the socket. You can close the connection any time by pressing *CTRL-C*.


#### Step 3: Accessing the Notebook
Once the tunnel is set up, you can access the Jupyter notebook by directing your favorite web browser to the URL in the *.err*-file from **step 1**. This could, for example, be:

```
http://localhost:8888/?token=0ef77dda74cag9274nc550ae49c55cb1c25ded2a6a0
```
    
Like in **step 2** before, you need to adjust the `8888` according to your desired local port. Keep in mind, that the message in the *.err*-file will always use port `8888`, so you might have to adjust it. The long string after `token=` is a security measure to prevent others from accessing your notebook.


#### Closing remarks
* Please do not let your notebooks run while not using them, as they occupy valuable resources.
* Closing the notebook does not mean the job will be closed, too. So please do not forget to cancle your respective jobs with `scancel <job-ID>` when done.
* If you accidentally close the forwarding from step 2, you can simply repeat step 2 to reestablish connection.
* If you get an error, that a port is already being used you can simply change the port. Further, you can close any process using a port with `kill $(lsof -t -i:<port-number>)`. 



















