---
title: "Frequently Answered Questions"
layout: default
nav_order: 9
---

In case of questions, please contact us at **pleiades@uni-wuppertal.de**

> **Q:** Can I get access to the cluster?
>
> **A:** Yes, if you are part of one of the involved groups. There are also limited resources for any other member of the University of Wuppertal. You can also consult our [Quick Reference Card](https://uni-wuppertal.sciebo.de/s/zV3kmj8Um6G5DAi/download) to study the access requirements. To request an account, fill out [this form](http://pleiades.uni-wuppertal.de/fileadmin/physik/pleiades/Accountantrag_072022.pdf).


> **Q:** How do I apply for an account?
>
> **A:** Fill out the [application form](http://pleiades.uni-wuppertal.de/fileadmin/physik/pleiades/Accountantrag_072022.pdf) and send it to **pleiades@uni-wuppertal.de**.


> **Q:** How can I use software on the cluster?
>
> **A:** There is a range of centrally provided software (also see our [software documentation](software)). Users and groups typically install their own software additionally in their `HOME` directories.
 
 
> **Q:** Can you install a specific software for me?
>
> **A:** If the software is interesting to other users/groups and is mentioned on the [list of supported software by EasyBuild](https://docs.easybuild.io/en/latest/version-specific/Supported_software.html), we can give it a try. We cannot promise to fulfill any installation or update request, since managing software is a very time consuming task, but we will respond to requests on a best effort basis.
 
 
> **Q:** Why is loading software modules so slow?
>
> **A:** Software modules are shipped via our BeeGFS shared file system. It can happen that running computations produce a heavy load on BeeGFS, which negatively affects the performance.
 
 
> **Q:** How can I do file transfers?
>
> **A:** See our [filetransfer documentation](filesystem/filetransfers).
 
 
> **Q:** Where should I compile my programs?
>
> **A:** Our login nodes have different hardware than our compute nodes (CPUs, InfiniBand). This is why it is a good idea to generally **compile where you compute**.
 
 
> **Q:** Do I have to make sure that I don't use too many resources?
>
> **A:** Yes and no. You should always try to minimize core-hours and number of jobs in order to be efficient. But if your computations are a requirement for your research, you can submit the jobs in any case. Slurms fair-share system will automatically ensure a fair scheduling of jobs between users in a group and between all groups.
 
 
> **Q:** Why is my job not starting?
>
> **A:** The `squeue` command has a `Reason` column, providing more information of why pending jobs are not starting. Maybe there are jobs with a higher priority. Some requests are impossible to schedule, because of conflicting requirements, e.g. more than 3 days on the `normal` partition, too many CPU cores per Node (max 64), etc..
