---
title: Pangeo Environment Modelserver
author: Wesley
toc: true
toc_sticky: true
excerpt: How to add packages to the pangeo environment on modelserver
---

# Introduction

A python environment is provided on modelserver so that everyone can work with the same packages. This environment is the `pangeo` environment visible inside jupyterhub.

# Adding packages

<div class='alert alert-danger'>
    This will changes packages system wide and not just for your user please bear in mind other people are using the system and updating all packages can break things.
</div>

To add packages to the environment you need to:

1. Login as root `sudo su root` and provide your password (contact sysadmin to become root on the machine)
1. Run `. /etc/profile.d/conda.sh` (yeah this is annoying but we need to add the conda shell scripts)
1. Run `conda activate python` the name of the "pangeo" kernel is actually `python`
1. Run your commands eg. `pip install ...` or `conda install ...` or `mamba install ...` (mamba = conda but quicker)