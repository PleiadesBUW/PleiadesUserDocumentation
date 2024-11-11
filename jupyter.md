---
title: "Jupyter"
layout: default
nav_order: 6
has_children: true
---

## Jupyter on PLEIADES
Jupyter is a powerful tool for data science and interactive computing.
Whether analyzing data, running simulations, or building models, Jupyters interactive environment and powerful tools make it easy to get work done.
PLEIADES' Jupyter documentation is divided into multiple sub-pages: [JupyterHub on PLEIADES](jupyter/jupyterHub) and [Custom Jupyter Notebooks on PLEIADES](jupyter/jupyter-nb_on_pleiades).  
Additionally, there are some notes on [Creating custom kernels with IPython](jupyter/jupyter-kernels).

The *JupyterHub on PLEIADES* page explains how to use the JupyterHub login to get automated JupyterLab servers batched through SLURM.
This means you can access the cluster resources through your browser, but you may have to wait a couple off minutes until the batch job is scheduled.
This option is recommended for those who prefer a quick and easy way to start working with Jupyter on the cluster.  

Alternatively, *Custom Jupyter Notebooks on PLEIADES* describes how to batch JupyterLab servers by yourself, providing full control over the `SBATCH` settings.
This option is recommended for those who have specific requirements or want to customize their JupyterLab environment.  
