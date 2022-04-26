---
title: FOAM
author: apohl
toc: true
toc_sticky: true
---

[last updated: 26 April 2022]

# Introduction

This tutorial describes how to run the Fast Ocean Atmosphere Model (Jacob, 1997) on the linux cluster of Univ. Bourgogne (Dijon). It should be easy to adapt to other clusters.

Please note that the FOAM source code is not freely available. The code developer, [Robert Jacob](https://www.anl.gov/profile/robert-l-jacob) plans to upload it on GitHub soon. In the meantime, please contact [me](Alexandre.pohl@u-bourgogne.fr) if you are interested in collaborating and obtaining the model code (and access to the cluster).

The tutorial is designed as follows: baseline instructions are given for the fully coupled model (with deep ocean), and additional details are provided where applicable for the slab mixed-layer ocean model.

# Requirements

Before starting anything, you need to correctly configure your environment by loading the appropriate modules:

```
module purge
module load intel/2018
module load netcdf/4.6.1/openmpi/2.1.2/intel/2018
module load cdo/1.9.2/intel/2018
module load nco/4.7.7/intel/2018
module load openmpi/2.1.2/intel/2018
```

# Installing and compiling

1. Download and uncompress the model source `tar xfvz foam1.5src.tar.gz` in your home directory `/user1/crct/zz9999zz`

2. On the CCUB cluster, you should be all set with libraries correctly linked in the `Makefile` on lines 190–192:

```
# CCUB
NETCDF_INC= -I/soft/c7/netcdf/4.6.1/openmpi/2.1.2/intel/2018/include
NETCDF_LIB= -L/soft/c7/netcdf/4.6.1/openmpi/2.1.2/intel/2018/lib -lnetcdff -L/soft/c7/phdf5/1.8.20/openmpi/2.1.2/intel/2018/lib -pthread -L/soft/c7/netcdf/4.6.1/openmpi/2.1.2/intel/2018/lib -lnetcdf -lnetcdf
NC_LIBS = $(NETCDFC_LDFLAGS) -lnetcdff
```

3. `Make clean`

4. Compile: `Make foam`. Everything going well, you should get an executable called `foam1.5`, which you should move into your home root directory (`mv foam1.5 ../.` or alternatively `mv foam1.5 /user1/crct/zz9999zz/`.

<div class='alert alert-info'>
For FOAM slab: `Make foamslab` and then `mv foam1.5 /user1/crct/zz9999zz/foam1.5.slab` (renaming avoids confusions with the executable of the fully coupled model).
</div>

Now, the model is compiled and ready to run. Let's see how to launch an experiment from a directory with all boundary and initial conditions ready. We'll see how to create these boundary and initial conditions later.

# Running an experiment from existing boundary and initial conditions

# Creating boundary and initial conditions

## Creating boundary conditions

## Creating initial conditions

# Debugging

# Checking for equilibrium and generating output files

## Checking for equilibrium

## Generating output files

# Looking at the model output

# References using FOAM



