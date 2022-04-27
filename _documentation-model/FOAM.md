---
title: FOAM
author: apohl
toc: true
toc_sticky: true
---

A FOAM User-guide at U. Bourgogne.

This page is not intended to replace the officiel [FOAM user guide](https://wiki.mcs.anl.gov/FOAM/index.php/Main_Page), but rather to provide additional / alternative material.

Information on the CCUB cluster can be found [here](https://ccub.u-bourgogne.fr/dnum-ccub/spip.php?article959).

[UNDER CONSTRUCTION; last updated: 26 April 2022]

# Introduction

This tutorial describes how to run the Fast Ocean Atmosphere Model (Jacob, 1997) on the linux cluster of Univ. Bourgogne (CCUB, Dijon). It should be easy to adapt to other clusters.

Please note that the FOAM source code is not freely available. The code developer, [Robert Jacob](https://www.anl.gov/profile/robert-l-jacob), plans to upload it on GitHub soon. In the meantime, you can contact [me](https://alexpohl.github.io/) if you are interested in collaborating, obtaining the model code and accessing the CCUB cluster.

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

For comparison, here should be the result of the `module list` command:

```bash
Currently Loaded Modulefiles:
 1) netcdf/4.6.1/openmpi/2.1.2/intel/2018   4) intel/2018
 2) cdo/1.9.2/intel/2018                    5) openmpi/2.1.2/intel/2018
 3) nco/4.7.7/intel/2018
```

Remark: At some point, following cluster module updates, we will probably have to change some modules, which will require recompiling the code with these modules and loading the same version afterwards.

# Installing and compiling

1. Download and uncompress the model source `tar xfvz foam1.5src.tar.gz` (not freely available to date, see page header) in your home directory `/user1/crct/zz9999zz` (replace `zz9999zz` with your CCUB login).

2. On the CCUB cluster, you should be all set with libraries correctly linked in the `Makefile` on lines 190–192:

```fortran
# CCUB
NETCDF_INC= -I/soft/c7/netcdf/4.6.1/openmpi/2.1.2/intel/2018/include
NETCDF_LIB= -L/soft/c7/netcdf/4.6.1/openmpi/2.1.2/intel/2018/lib -lnetcdff -L/soft/c7/phdf5/1.8.20/openmpi/2.1.2/intel/2018/lib -pthread -L/soft/c7/netcdf/4.6.1/openmpi/2.1.2/intel/2018/lib -lnetcdf -lnetcdf
NC_LIBS = $(NETCDFC_LDFLAGS) -lnetcdff
```

3. `Make clean` to clean compiling directory from previous iterations.

4. Compile: `Make foam`. Everything going well, you should get an executable called `foam1.5`, which you should move into your home root directory (`mv foam1.5 ../.` or alternatively `mv foam1.5 /user1/crct/zz9999zz/`.

__For FOAM slab: `Make foamslab` and then `mv foam1.5 /user1/crct/zz9999zz/foam1.5.slab` (renaming avoids confusion with the executable of the fully coupled model).__

Now, the model is compiled and ready to run. Let's see how to launch an experiment from a directory with all boundary and initial conditions ready. We'll see how to create these boundary and initial conditions later.

In the following, you'll need an ensemble of files, which are downloadable [here](https://MISSING-LINK)

# Running an experiment from existing boundary and initial conditions

## Directory with boundary conditions and initial conditions

In the archive that you previously downloaded [here](https://MISSING-LINK), you will find a directory `BC_300rd_T36.tar.gz` containing all boundary and initial conditions required to run an random experiment at 300 Ma (taken from [this dataset](https://zenodo.org/record/5780097#.Ymjy9fNByCd). Place the (uncompressed directory in your `WORKDIR` (the place where you will want to run your simulations; this is a Scratchdir, although files are not automatically deleted after a given duration, as is done on other clusters) at the following location (while creating required directories): `/work/crct/zz9999zz/foam/phanero/300rd/300rd_T36/BC_300rd_T36` (or another location of your choice, I propose typical paths for illustration).

## Run directory

Now, let's create the directory where the FOAM model will run. For the purpose of illustration, we will run an experiment at 2240 ppm CO2 (i.e., 8 x 280 ppm = 8 PAL pCO2, preindustrial atmospheric level) with null eccentricity. Let's call it `EcN_8X`.

### Create run directory

Create run directory `mkdir /work/crct/zz9999zz/foam/phanero/300rd/300rd_T36/EcN_8X`, enter that directory `cd /work/crct/zz9999zz/foam/phanero/300rd/300rd_T36/EcN_8X` and copy the content of downloaded directory `run_directory` there (`cp run_directory/* /work/crct/zz9999zz/foam/phanero/300rd/300rd_T36/EcN_8X/.`).

Now, let's edit all requested files.

### File `run_params`

File `run_params` gives the run duration and paths. In this file, replace `zz9999zz` with your login. All other info is OK (see below).

```fortran
RESTFRQ: 360
HISTFRQ: 360
FILTPHIS: T
INITIAL: T
PREFIX: /work/crct/zz9999zz/foam/phanero/300rd/300rd_T36/EcN_8X
STORAGE: /work/crct/zz9999zz/foam/phanero/300rd/300rd_T36/EcN_8X
TIME_INV: /work/crct/zz9999zz/foam/phanero/300rd/300rd_T36/BC_300rd_T36
FINISHED: 0
RUNLNG: 720000
```
- `RESTFRQ`: the FOAM model year is 360 days.
- `HISTFRQ`: writing model output every year / 360 days.
- `FILTPHIS` and `INITIAL` set to TRUE for a new (i.e., not restarted) simulation (we filter boundary conditions).
- `PREFIX`: path to run directory.
- `STORAGE`: path to run directory.
- `TIME_INV`: path to boundary conditions directory.
- `FINISHED`: last simulated year (0 for a new simulation).
- `RUNLNG`: run duration, in days. 720 000 days is 2000 years (of 360 days each, see `RESTFRQ`).

Caution: too long a path (e.g., `/work/crct/zz9999zz/foammmmmmmmm/phanero/300rd/300rd_T36/EcN_8X`) will make the model crash with no clear error message. Be aware.

### File `atmos_params`

File `atmos_params` defines an ensemble of parameter values, output variables and file names. More importantly, it also defines atmospheric pCO2, the solar constant and orbital configuration. 

For the purpose of this turorial, you do not need to change anything.

```fortran
 &CCMEXP
 PRIMARY='RELHUM',
 EXCLUDE='DC01','TS2','TS3','VD01','TS4','DTH','DTV','CMFDT','CMFDQ','DTCOND','CNVCLD','TAUGWX','QPERT','TPERT','TAUX','TAUY','UTGW','VTGW','VVPUU','NTRM','NTRN','NTRK,'NDBASE','NSBASE','NBDATE','NBSEC','MDT','MHISF','CURRENT_MSS','FIRST_MSS','INIT_MSS','TIBND_MSS','SST_MSS','OZONE_MSS','DATE','DATESEC',
 CTITLE = 'PCCM3-UW case: default',
 NCDATA = 'SEP1.R15.nc',
 BNDTI = 'tibds.R15.nc',
 BNDTVS = 'tvbds.R15.nc',
 BNDTVO = 'ozn.R15.nc',
 IRT=0,
 NSREST=3,
 NREVSN='rest',
 NSWRPS='passwd',
 NSVSN='rstrt',
 NDENS  =     1,
 NNBDAT = 000101,
 NNBSEC = 0,
 NNDBAS = 0,
 NNSBAS = 0,
 MFILT  =  1,
 DTIME  =  1800.,
 IRADSW =   -1,
 IRADLW =   -1,
 IRADAE =   -12,
 SSTCYC = .T.,
 OZNCYC = .T.,
 DIF4=2.E16,
 ACCRST = .T.,
 CO2VMR = 2240e-06,
 CH4VMR = 1.714000e-06,
 SCON = 1368e+03,
 ECCEN = 0,
 OBLIQ = 22,
 MVELP = 90,
 DAYP = 0,
 &END
```

- `CO2VMR = 2240e-06`: Atmospheric CO2 is set to 2240 ppm.
- `1368e+03`: The solar constant is set to Modern (1368 W m-2).
- `ECCEN = 0`: Eccentricity is zero.
- `OBLIQ = 22`: Obliquity is 22°.
- `MVELP = 90` and `DAYP = 0`: longitude of perihelion which has no impact here due to the null eccentricity (more info [here](https://wiki.mcs.anl.gov/FOAM/index.php/Users_Guide)).

### File `pbs.foam16p.script`

File `pbs.foam16p.script` is a bash script permitting to submit the simulation in batch mode.
For the purpose of this turorial, you do not need to change anything.

```fortran
#!/bin/bash
#$ -N j300RD8
#$ -pe dmp* 32
#$ -q batch

module purge
module load intel/2018
module load netcdf/4.6.1/openmpi/2.1.2/intel/2018
module load cdo/1.9.2/intel/2018
module load nco/4.7.7/intel/2018
module load openmpi/2.1.2/intel/2018

mpirun foam1.5 run_params

#qsub pbs2
```
- `#$ -N j300RD8`: The job name, just convenient.
- `#$ -pe dmp* 32` and `#$ -q batch`: Simulation ran in batch mode on 32 cores in parallel. More info [here](https://ccub.u-bourgogne.fr/dnum-ccub/spip.php?article959).

Modules are loaded (the same modules as defined at start, used also to compile the model executable), and the executable of the fully coupled model (`foam1.5`) is launched using the parameters found in `run_params`.

### Linking the executable

While in the run directory, create a symbolic link (to avoid filling the disk) to the compiled executable: `ln -s /user1/crct/zz9999zz/foam1.5 .`

### Creating history and restart directories

While in the run directory, create the history and restart directories, for each model component (ocean, atmosphere and coupler):

- `mkdir history`
- `mkdir history/atmos`
- `mkdir history/ocean`
- `mkdir history/coupl`
- `mkdir restart`
- `mkdir restart/atmos`
- `mkdir restart/ocean`
- `mkdir restart/coupl`

Model output will be written in `history` (`atmos` for the atmosphere etc.), while restart files will be written in `restart` (`atmos` for the atmosphere etc.).

## Launching the simulation

While in the run directory, submit the job (`qsub pbs.foam16p.script`). You can check the run status using `qstat`(and will be happy at that time to see a proper run name, defined above).
To abort the run, `qdel job-ID`, with `job-ID`being the job number (e.g. 7013515) obtained with the command `qstat`.

It will take approximatively 4 days to run for 2000 model years. Output is written in the `history`output directories (one file every 3 to 4 minutes). Useful information can be found during the run in output files `om3.out.4_8.0`and `pccm.out.0720000`.

In case the simulation crashes, see section below.

# Creating boundary and initial conditions

We previously ran an experiment based on a provided direcvtory of boundary and initial conditions. It's now time to see how to create those from scratch.

## Creating boundary conditions

Boundary conditions are model parameters that do not evolve during the simulation. They notably define the continental configuration, topography and bathymetry, pCO2, solar luminosity, orbital configuration etc.

### Slarti boundary conditions

Boundary conditions are created using a graphical interface named Slarti. The software `Slarti.jar` is included in the files you downloaded. It can be run on MacOS (`open Slarti.jar`) and Linux (and probably Windows, but who uses Windows?). I suggest using Slarti on your local computer for speed purposes when using the mouse.

Slarti takes in input a DEM in the form of a netcdf with 128 x 128 grid points. One example file is provided with this turorial: `Topobathy_300eb_postslarti_cor.nc`.

Remark: you can easily create a similar input file based on the paleoDEMS of [Scotese and Wright (2018)](https://www.earthbyte.org/paleodem-resource-scotese-and-wright-2018/), which you may want to regrid on a 128 x 128 grid using the `cdo remapbil` tool (module available on the CCUB as well).
     
1. `File/New`, 128x128, R15, Bathymetry/Topography, `Topobathy_300eb_postslarti_cor.nc`, NetCDF, no Optional Input, click OK (see screenshot below).

<img src="/assets/images/documentation/model/FOAM_screenshot_Slarti.png"
     alt="Slarti Screenshot"
     style="float: left; margin-right: 10px;" />
     
2. `Please enter the bathymetry/topography variable name`: `TOPO` in this example (you can get this info using the nco tool `ncdump -h Topobathy_300eb_postslarti_cor.nc`; nco available on the CCUB as well).

3. `View-Edit/Mask`: You can `Highlight ocean cells with no current` and alter the land-sea mask manually. During this step, you have to make sure not to create any lakes, and that oceanic gateways are large enough to permit water flow (or just close them if that's better). Don't forget to `Compare and match files` and `File/Save` regularly, see step 4.

4. `File/Save`; Save for Coupled Run > OK > OK > lakes can be found at this stage, close window and `Continue` > OK > Next > OK > OK (you may need to enlarge the window to see the button appear), define path (e.g., `/Users/username/Desktop/300rd`) and OK. I suggest saving on the Desktop, since Slarti seems to have writing rights issues in some other directories.

5. `View-Edit/Vegetation`: Leave unchanged for a theoretical latitudinal distribution like today, or alter for deep-time periods (e.g. rocky desert in the Cambrian in the absence of land plants).

6. `View-Edit/Bathymetry`: Best changing level by level in step 7.

7. `View-Edit/Bathymetry Level View`: Level by level, make sure to avoid isolated ocean grid points where salt could accumulate, which would make the model drift and ultimately crash.

8. `View-Edit/Topography`: can be altered here, but keep in mind that the atmosphere will see a lower-resolution version (only shown when saving). What you see in the topography seen by the coupler.

9. `View-Edit/River flow`: define the water routing manually, while making sure to avoid creating lakes. Please remember to `Compare and match files` when switching from one window to another one. For instance, changing the topography will automatically alter the river flow. Be aware of this. At any time, you can manually `List river flow lakes`. No lakes should be found in the final boundary conditions. If lakes are found, you have to get rid of them.

10. At any time, you can save your boundary conditions in their current state and resume editing later. Just open Slarti and `File/Open` the `.case` file included in your directory of saved boundary conditions.

__The slab model has no routing and lakes can exist. However, it means that you will not be able to run the fully coupled model using the same directory of boundary conditions. I suggest always preparing clean boundary conditions for the coupled model, even if you wanna run the slab version.__

Importantly, you can use the directory of boundary conditions provided with this tutorial (`BC_300rd_T36.tar.gz`) to look at the expected Slarti output. To that purpose, open Slarti and `File/Open`, find uncompressed directory `BC_300rd_T36` and open file `300rd.case`. 

### Gathering required files

Let's gather all required files in a directory (just as you previous used directory `BC_300rd_T36` (uncompressed from `BC_300rd_T36.tar.gz`).

1. Create a new directory `/work/crct/al1966po/foam/phanero/300rd/300rd_T36/BC_300rd_T37`.

2. Copy a series of requested files available in the files you previously downloaded: `cp TOADD/* /work/crct/al1966po/foam/phanero/300rd/300rd_T36/BC_300rd_T37/.`

3. Copy the Slarti output files. These files should now be saved on your local computer. Use `rcp` to send them on the cluster. `scp 300rd/* zz9999zz@krenek2002.u-bourgogne.fr:/work/crct/al1966po/foam/phanero/300rd/300rd_T36/BC_300rd_T37/.`.

4. Enter the directory `cd /work/crct/al1966po/foam/phanero/300rd/300rd_T36/BC_300rd_T37/` and rename 2 files: `mv tibds.R15.* tibds.R15.nc`and `mv initial.R15.* SEP1.R15.nc`.

Now, your boundary conditions are ready. In order to test them in this tutorial, we can simply adapt the paths used in the `EcN_8X` experiment that we previously set up. To that purpose, just edit file `run_params`: `TIME_INV` should new read `/work/crct/al1966po/foam/phanero/300rd/300rd_T36/BC_300rd_T37`.

Make sure (with `qstat`) that the simulation is not currently running (or stop it using `qdel`) and launch it with `qsub pbs.foam16p.script`.
 
## Creating initial conditions

Initial conditions define model 'starting' conditions, for instance ocean temperature and salinity, which are expected to change during the model run.

You will be interested in 2 specific files defining ocean initial salinity and temperature.

__With the slab model, ocean dynamics is not resolved. Please ignore this paragraph on the model initial conditions__

### Field of initial salinity

The default field of initial salinity is Modern and defined in file `BC_300rd_T37/om3.salt24`.

For paleo application, you will be interested in starting with a globally uniform ocean salinity field. Such a file, with a global salinity of 35 permil, is found in the files your previously downloaded, here: `UTIL/om3.salt35`. TO use it:

1. In your directory of boundary conditions (`/work/crct/al1966po/foam/phanero/300rd/300rd_T36/BC_300rd_T37`), delete default file: `rm om3.salt24`.

2. Copy desired file: `cp UTIL/om3.salt35 /work/crct/al1966po/foam/phanero/300rd/300rd_T36/BC_300rd_T37/.'

3. Create symbolic link: `ln -s om3.salt35 om3.salt24`. Actually, you could just `mv om3.salt35 om3.salt24` or `cp om3.salt35 om3.salt24`, but the symbolic link permits keeping track of the file you used. 

### Field of initial ocean temperature

Similarly, the default field of initial temperature is Modern and defined in file `BC_300rd_T37/om3.temp24` and you can replace it with any file found in directory `UTIL` (e.g., `om3.temp24_20_5_1_1`). These files are all named using the same pattern: `om3.temp24_`, followed by: equatorial surface temperature, equatorial thermocline temperature, sea-bottom temperature and polar temperature (both supposed to be identical due to deep-water formation at the poles).

I suggest to use a warm initial ocean (around 3 degrees warmer than expected equilibrium temperature) in order to ensure intense oceanic convection and a rapid deep-ocean temperature equilibrium.

The FOAM model climatic sensitivity being around 3°C, I provide intiial temperature files very ~3 degrees. You can also create your own intial temperature fields using the Matlab and Python routines provided in directory `UTIL/NEWEQ`.

1. First run the Matlab script `raw_om3_temp24.m`to generate a NetCDF file with the expected temperature data.

2. Then, run the Python script `AP_theoretical_nc_to_om3.py` to convert the NetCDF into a binary file `om3.temp24` that can be read by FOAM.

# Checking for equilibrium and generating output files

## Checking for equilibrium

## Generating output files

# Debugging

Different cases:

## Nothing bad happened

## Timestep issue (sea ice, warm climate))

## Salt anomaly

# Looking at the model output

# References using FOAM



