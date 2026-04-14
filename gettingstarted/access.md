---
title: "Access"
layout: default
parent: Getting Started
nav_order: 1
---

## Getting Started: Access
### Getting an account
If you belong to one of the groups participating in PLEIADES, you can get an account by filling out [this form](https://pleiades.uni-wuppertal.de/fileadmin/physik/pleiades/Accountantrag_032024.pdf).
If your group was **not** involved in PLEIADES, you can still get access, but please contact the support team before submitting an account request.
In general, you can consult our [HPC.nrw Quick Reference Card](https://uni-wuppertal.sciebo.de/s/zV3kmj8Um6G5DAi/download) which outlines the access conditions and procedures.
Members of the University requiring an account for research or their thesis project can typically get access.

Accounts are valid for a given period (max. 3 years).
At the end, you will receive automatic messages about your account life time and can either contact us about an extensions, if necessary.
**The extension request should contain an exact extension date and involve your group supervisor**, such that we know that you are still part of your group.

### Account details: You will get the access details by an email after granting access rights
> Dear `<user>`,
>
> your PLEIADES account has been created:
>
> &nbsp;&nbsp;&nbsp;&nbsp;username -> `<user>`  
> &nbsp;&nbsp;&nbsp;&nbsp;password -> `<password>`
>
> Your home directory on the cluster is: `/beegfs/<user>`
>
> ...
>
> If you have questions, feel free to contact us at pleiades@uni-wuppertal.de (or reply to this mail).
>
> Kind Regards  
> &nbsp;&nbsp;&nbsp;&nbsp;Your PLEIADES Team

### Questions/Support
In case of questions and problems, please use the following email address:

**pleiades{at}uni-wuppertal.de**


### First Login and password change
You will receive your initial password from the administrators after your group leader has countersigned the user application.
Please change your initial password on any PLEIADES login machine by using this command:

```bash
$ passwd
Changing password for user USERNAME.
Current Password:
[...]
```


### Interactive JupyterHub
As an alternative to a terminal-based SSH login, we offer a [JupyterHub](../jupyter) instance that starts an interactive JupyterLab session from your browser.
It automatically allocates a small set of resources that are meant for interactive usage.
> **Note:** The JupyterHub is only available from within the university network. If you are outside and need access, use the ZIMs webvpn.


### SSH Login
We recommend to create a **password protected** [ssh-key](https://hpc-wiki.info/hpc/Ssh_keys) pair to authenticate on login.
Additionally you can define in your local `~/.ssh/config`:
```
Host fugg1
    User <USERNAME>
    Hostname fugg1.pleiades.uni-wuppertal.de
    IdentityFile ~/.ssh/<KEYNAME>
```

This way, simply using `ssh fugg1` will log in correctly to the cluster.
More info about ssh keys is available in the [corresponding github documentation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent).

> **Note:** This approach is also more secure, since mis-typing the URL for `uni-wuppertal.de` could expose your credentials to a malicious server that is not in our control.


### Login Nodes (all users except "whep" users)
There are two login machine from which the cluster can be operated. They are:

```
fugg1.pleiades.uni-wuppertal.de
fugg2.pleiades.uni-wuppertal.de
```

These nodes can be used to develop and test code. The node is mostly used to submit Slurm batch jobs to the PLEIADES cluster.
This machine runs Alma Linux 9.
You can login on it using your username, which will be provided by us.
Due to massive attacks from all over the world, SSH access is limited to IPs from inside the university's network (`132.195.0.0/16`). In addition, a protection system is used that blocks IP numbers which have been used with several unsuccessful logins. So if you mistype your credentials too often, you will be locked out for a while.

A good practice for using ssh regularly is to setup ssh-keys on your local machine and use

```bash
# On your local computer (assuming Linux):
# ----------------------------------------
# if you don't have a key, e.g. ~/.ssh/id_ed25519.pub, create a new one
# and set a password!
ssh-keygen -t ed25519
# copy the ssh key to fugg1
ssh-copy-id USERNAME@fugg1.pleiades.uni-wuppertal.de
# Login will now use the ssh key
ssh USERNAME@fugg1.pleiades.uni-wuppertal.de
```

(from your local machine) to enable a key-based login on the frontend.


### Login Nodes (whep users)
The login mechanism for whep users is the same as for all other users, except for the login nodes.
Since the beginning of 2024, there are also two new machines running Alma Linux 9:
```bash
up.pleiades.uni-wuppertal.de
down.pleiades.uni-wuppertal.de
```

**Only whep users can log into up, and down!**


### Advanced SSH logins

The following configurations are optional and intended to simplify access to the cluster.
Users may choose between a **basic configuration (recommended)** and an **advanced configuration (ProxyJump)** based on their workflow.

#### Way 1: Basic SSH Configuration (Recommended)

Add the following to your `~/.ssh/config` file:

```
Host fugg1 fugg2
    Hostname %h.pleiades.uni-wuppertal.de

Match Host fugg1.pleiades.uni-wuppertal.de,fugg2.pleiades.uni-wuppertal.de
    User user
    IdentityFile ~/.ssh/pleiades
    ControlMaster no
    ControlPath ~/.ssh/control-%h-%p-%r
    ControlPersist 2h
```

##### Advantages:
- Simple and transparent connection workflow
- Easier to debug connection issues
- Clearly separates login nodes and compute nodes
- Recommended for most users

##### Typical Usage:

```
ssh fugg1
```

Then:
- allocate a compute node (e.g., using `srun` or `salloc`)
- establish port forwarding if required. For more information, see the [ssh](https://man7.org/linux/man-pages/man1/ssh.1.html) manual.

> **Important:**
>
> The above described **Way 1** allows users to connect from a *local machine* to a *Login node* using **SSH**.

> **Typical Connection Flow:**
>
> - Local Machine -> Login Node (`fugg*`) -> SLURM Allocation -> Compute Node (`wn*`) -> Exit (release resources)

Alternatively, the following advanced configuration may be used:

#### Way 2: Advanced SSH Configuration (ProxyJump)

This configuration enables direct SSH access to compute nodes via the login node using the ProxyJump mechanism.

Add the following to your `~/.ssh/config` file:

```
Host fugg1 fugg2
    Hostname %h.pleiades.uni-wuppertal.de

Match Host fugg1.pleiades.uni-wuppertal.de,fugg2.pleiades.uni-wuppertal.de
    User user
    IdentityFile ~/.ssh/pleiades
    ControlMaster no
    ControlPath ~/.ssh/control-%h-%p-%r
    ControlPersist 2h

Host wn21*
    Hostname %h.pleiades.uni-wuppertal.de

Match Host wn21*.pleiades.uni-wuppertal.de
    User user
    IdentityFile ~/.ssh/pleiades
    ProxyJump fugg1
    StrictHostKeyChecking accept-new
```

> **Note:**
>
> Use either `fugg1` or `fugg2` as the jump host.
> Avoid specifying multiple jump hosts simultaneously to ensure deterministic behaviour, thereby ensuring predictable routing (`local -> fugg1 -> wn21*`)

##### Advantages:
- Enables direct access to compute nodes
- Reduces the number of manual SSH steps required
- More efficient for repeated or advanced usage

##### Typical Usage:

> **Warning:**
>
> - Ensure that the compute node **`wn21101`** is allocated and actively running (e.g., via `srun` or `salloc`) before connecting.
> - Connection will fail if the job has not started or has already terminated.
> - Release allocated resources after completion by exiting the compute node (`exit`).


> **Hint:**
>
> The hostname (e.g., `wn21101`) is assigned dynamically by the scheduler and must be obtained from `squeue --me` or the job output.

```
ssh wn21101
```

or with port forwarding:

```
ssh -L 8080:localhost:8080 wn21101
```

> **Important:**
>
> The above described **Way 2** enables direct **SSH** access to a *Compute node* from a *local machine* using ProxyJump.
> - Step 1: Ensure access to a *login node* from your *local machine* (See **Way 1**).
> - Step 2: Allocate a *compute node* using `salloc` or `srun`.
> - Step 3: Verify that the *compute node* is allocated and in a running state using `squeue --me`.
> - Step 4: Open a new terminal and connect to the allocated *compute node* via **SSH**.
> - Step 5: Once the work is completed, exit the *compute node* using the `exit` command to release resources for other users.

> **Typical Connection Flow:**
>
> - Local Machine -> Login Node (`fugg*`) -> SLURM Allocation -> Compute Node (`wn*`)
> - Local Machine (new terminal) -> ProxyJump (`fugg*`) -> Compute Node (`wn*`)
> - Exit (release resources)

> **Summary of SSH Connections:**
>
> - The login nodes (`fugg*`) remain the gateway to the cluster, even when using ProxyJump
> - The advanced configuration is optional and intended for experienced users
> - Both methods provide equivalent functionality; ProxyJump primarily improves convenience

> **Recommendation:**
>
> - Use **Way 1 (Basic SSH)** if you are new to the cluster or debugging issues
> - Use **Way 2 (ProxyJump)** for repeated workflows and automation
