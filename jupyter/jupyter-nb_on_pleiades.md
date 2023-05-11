---
title: "Jupyter: Custom Jupyter Notebooks on PLEIADES"
---

## Jupyter: Custom Jupyter Notebooks on PLEIADES
PLEIADES does provide easy access to JupyterLab through a hosted JupyterHub instance. However, this ease of use comes with limited resources available for selection. If more resources are required, it is still possible to run a custom interactive Jupyter Notebook on the worker nodes with a few workarounds. It is important to note that depending on the method chosen to access the notebook, it may expose your work and potentially give access to your files to other users. Therefore, please stick to the provided approach. It is also important to clean up after yourself and close any unneeded Jupyter sessions to prevent others from being unable to access necessary computing resources.

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
2022a GCCcore/11.3.0 JupyterHub/3.1.1 JupyterLab/3.5.0
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
Worker nodes cannot be accessed from outside. This means, you cannot directly tunnel through socket `jupyter_wn.socket` from your local machine. Instead, you have to use one of the login nodes (*fugg1* or *fugg2*) as a jump host (inserting the worker node from **step 1**):

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

#### Using virtual environments for custom IPython kernels

It is possible to define your own IPython kernels based on virtual environments created and managed through *Conda* or a combination of *pip* and *venv*. We describe this in detail in our section on the [custom IPython kernels](./jupyter-kernels.md).

#### Closing remarks
* Please do not let your notebooks run while not using them, as they occupy valuable resources.
* Closing the notebook does not mean the job will be closed, too. So please do not forget to cancle your respective jobs with `scancel <job-ID>` when done.
* If you accidentally close the forwarding from step 2, you can simply repeat step 2 to reestablish connection.
* If you get an error, that a port is already being used you can simply change the port. Further, you can close any process using a port with `kill $(lsof -t -i:<port-number>)`. 





















