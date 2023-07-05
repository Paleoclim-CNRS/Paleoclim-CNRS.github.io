---
title: JupyterHub
author: Wesley
excerpt: General information about the jupyterhub CEREGE deployement
---

# Introduction

[Jupyter](https://jupyter.org) is a project aimed at providing an interactive computing environment. It provides a way to run code inside a kernel from a browser. There exist a number of different kernels availible, written in different languages and it is trivial to extend base kernel with custom computing enviroment. There are two standard web interfaces, [Jupyter Notebook](https://jupyter-notebook.readthedocs.io/en/stable/) and [Jupyter Lab](https://jupyterlab.readthedocs.io/en/latest/), as well as a certain number of spinoffs of note [nteract](https://nteract.io).

[JupyterHub](https://jupyter.org/hub) brings the power of notebooks to a group of users. It is installed on a server therefore providing access to computational environments and resources without the need for users to carry out installation or maintenance tasks.

# Adding extensions

JupyterHub is installed into `/opt/jupyterhub` meaning if you need to have access to jupyter executable or the python used by jupyterhub (not the pangeo kernel) it is here: `/opt/jupyterhub/bin/`