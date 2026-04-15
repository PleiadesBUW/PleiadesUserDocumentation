---
title: "More on SSH"
layout: default
parent: Getting Started
nav_order: 5
---

### Advanced Secure Shell (SSH) logins

The following configurations are optional and intended to simplify access to the cluster.
Users may choose between a **basic configuration (recommended)** and an **advanced configuration (ProxyJump)** based on their workflow.

#### Way 1: Basic SSH Configuration (Recommended)

Add the following to your `~/.ssh/config` file:

```
Host fugg1 fugg2
    Hostname %h.pleiades.uni-wuppertal.de

Match Host fugg1.pleiades.uni-wuppertal.de,fugg2.pleiades.uni-wuppertal.de
    User username
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
    User username
    IdentityFile ~/.ssh/pleiades
    ControlMaster no
    ControlPath ~/.ssh/control-%h-%p-%r
    ControlPersist 2h

Host wn21*
    Hostname %h.pleiades.uni-wuppertal.de

Match Host wn21*.pleiades.uni-wuppertal.de
    User username
    IdentityFile ~/.ssh/pleiades
    ProxyJump fugg1
    StrictHostKeyChecking accept-new
```

> **Note:**
>
> Use either `fugg1` or `fugg2` as the jump host.
> Avoid specifying multiple jump hosts simultaneously to ensure deterministic behaviour, thereby ensuring predictable routing (`local -> fugg1 -> wn21*`)

##### Advantages:
- Enables direct access to compute nodes, allowing interaction with allocated (exclusive) resources that are not available on shared login nodes
    - Example: Run and access interactive services on compute nodes such as port forwarding for `VS Code` sessions
- Reduces the number of manual SSH steps required
- More efficient for repeated or advanced usage
- Ensures interactive workloads are executed on compute nodes instead of shared login nodes

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
