---
title: FOAM
author: apohl
toc: true
toc_sticky: true
---

[last updated: 26 April 2022]

# Introduction

This tutorial describes how to run the Fast Ocean Atmosphere Model (Jacob, 1997) on the linux cluster of Univ. Bourgogne (CCUB, Dijon). It should be easy to adapt to other clusters.

Please note that the FOAM source code is not freely available. The code developer, [Robert Jacob](https://www.anl.gov/profile/robert-l-jacob), plans to upload it on GitHub soon. In the meantime, you can contact [me](https://alexpohl.github.io/) if you are interested in collaborating, obtaining the model code and accessing to the CCUB cluster.

The tutorial is designed as follows: baseline instructions are given for the fully coupled model (with deep ocean), and additional details are provided where applicable for the slab mixed-layer ocean model.

# Requirements

Before starting anything, you need to correctly configure your environment by loading the appropriate modules:

```bash
module purge
module load intel/2018
module load netcdf/4.6.1/openmpi/2.1.2/intel/2018
module load cdo/1.9.2/intel/2018
module load nco/4.7.7/intel/2018
module load openmpi/2.1.2/intel/2018
```

# Installing and compiling

1. Download and uncompress the model source `tar xfvz foam1.5src.tar.gz` in your home directory `/user1/crct/zz9999zz` (replace `zz9999zz` with your CCUB login)

2. On the CCUB cluster, you should be all set with libraries correctly linked in the `Makefile` on lines 190–192:

```fortran
# CCUB
NETCDF_INC= -I/soft/c7/netcdf/4.6.1/openmpi/2.1.2/intel/2018/include
NETCDF_LIB= -L/soft/c7/netcdf/4.6.1/openmpi/2.1.2/intel/2018/lib -lnetcdff -L/soft/c7/phdf5/1.8.20/openmpi/2.1.2/intel/2018/lib -pthread -L/soft/c7/netcdf/4.6.1/openmpi/2.1.2/intel/2018/lib -lnetcdf -lnetcdf
NC_LIBS = $(NETCDFC_LDFLAGS) -lnetcdff
```

3. `Make clean`

4. Compile: `Make foam`. Everything going well, you should get an executable called `foam1.5`, which you should move into your home root directory (`mv foam1.5 ../.` or alternatively `mv foam1.5 /user1/crct/zz9999zz/`.

<div class='alert alert-info'>
For FOAM slab: `Make foamslab` and then `mv foam1.5 /user1/crct/zz9999zz/foam1.5.slab` (renaming avoids confusion with the executable of the fully coupled model).
</div>


Now, the model is compiled and ready to run. Let's see how to launch an experiment from a directory with all boundary and initial conditions ready. We'll see how to create these boundary and initial conditions later.

In the following, you'll need an ensemble of files, which are downloadable [here](https://MISSING-LINK)

# Running an experiment from existing boundary and initial conditions

## Directory with boundary conditions and initial conditions

In the archive that you previously downloaded, you will find a directory `BC_300rd_T36.tar.gz` containing all boundary and initial conditions required to run an random experiment at 300 Ma. Place the (uncompressed directory in your `WORKDIR` (the place where you will want to run your simulations; this is a `scratchdir`, although files are not automatically deleted) at the following location (while creating required directories): `/work/crct/zz9999zz/FOAMtutorial/phanero/300rd/300rd_T36/BC_300rd_T36`.

## Run directory

Now, let's create the directory where the FOAM model will run. For the purpose of illustration, we will run an experiment at 2240 ppm CO2 (i.e., 8x280 ppm = 8 PAL pCO2, preindustrial atmospheric level) with null eccentricity. Let's call it `EcN_8X`.

### Create run directory

Create run directory `mkdir /work/crct/zz9999zz/FOAMtutorial/phanero/300rd/300rd_T36/EcN_8X`, enter that directory `cd /work/crct/zz9999zz/FOAMtutorial/phanero/300rd/300rd_T36/EcN_8X` and copy the content of downloaded directory `run_directory` there (`cp run_directory/* /work/crct/zz9999zz/FOAMtutorial/phanero/300rd/300rd_T36/EcN_8X/.`).

### File `run_params`

In file `run_params` (below), replace `zz9999zz` with your login. All other info is OK.

```fortran
RESTFRQ: 360
HISTFRQ: 360
FILTPHIS: T
INITIAL: T
PREFIX: /work/crct/zz9999zz/FOAMtutorial/phanero/300rd/300rd_T36/EcN_8X
STORAGE: /work/crct/zz9999zz/FOAMtutorial/phanero/300rd/300rd_T36/EcN_8X
TIME_INV: /work/crct/zz9999zz/FOAMtutorial/phanero/300rd/300rd_T36/BC_300rd_T36
FINISHED: 0
RUNLNG: 720000
```
- `RESTFRQ`: the FOAM model year is 360 days.
- `HISTFRQ`: writing model output every year.
- `FILTPHIS` and `INITIAL` set to TRUE for a new (i.e., not restarted) simulation (we filter boundary conditions)
- `PREFIX`: path to run directory
- `STORAGE`: path to run directory
- `TIME_INV`: path to boundary conditions directory
- `FINISHED`: last simulated year (0 for a new simulation)
- `RUNLNG`: run duration, in days. 720 000 days is 2000 years (of 360 days each, see `RESTFRQ`).

# Creating boundary and initial conditions

## Creating boundary conditions

## Creating initial conditions

# Debugging

# Checking for equilibrium and generating output files

## Checking for equilibrium

## Generating output files

# Looking at the model output

# References using FOAM



