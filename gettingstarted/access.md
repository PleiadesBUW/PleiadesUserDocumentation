---
title: "Getting Started: Access"
---

## Getting Started: Access
### Getting an account
If you belong to one of the groups participating in PLEIADES, you can get an account by filling out [this form.](http://pleiades.uni-wuppertal.de/fileadmin/physik/pleiades/Accountantrag_072022.pdf)
If your group was **not** involved in PLEIADES, please ask your group leader to contact the support before submitting an account request or consult our [HPC.NRW Quick Reference Card](https://uni-wuppertal.sciebo.de/s/zV3kmj8Um6G5DAi/download).


### Questions/Support
In case of questions and problems, please use the following email address:

**pleiades{at}uni-wuppertal.de**


### First Login and password change
You will receive your initial password from the administrators after your group leader has countersigned the user application.
Please change your initial password on any Pleiades login machine by using the

```bash
$ passwd
```

command.


### SSH Login
We recommend to create a password protected [ssh-key](https://hpc-wiki.info/hpc/Ssh_keys) pair to authenticate on login.
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


### Login (all users except "whep" users)
There are two login machine from which the cluster can be operated. They are:

```
fugg1.pleiades.uni-wuppertal.de
fugg2.pleiades.uni-wuppertal.de
```

This node can be used to develop and test code. Once this is finished jobs can be submitted to the pleiades cluster. This machine runs CentOS 7. You can login on it using your username, which will be provided by us.
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


### Login (whep users)

The login mechanism for whep users is the same as for all other users, except for the login nodes. There are 2 login nodes running CentOS 7 (recommended)

```bash
higgs.pleiades.uni-wuppertal.de
top.pleiades.uni-wuppertal.de
```

**Only whep users can log into higgs and top!!!**
