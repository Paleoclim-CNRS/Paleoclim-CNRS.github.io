---
author: Anthony
title:  Set up PALEO PISCES offline
excerpt: Installing and configuring Paleo Pisces offline
toc: true
---

[![](https://img.shields.io/static/v1?label=Code&message=here&color=lightgrey&style=flat-square&logo=github)](https://github.com/Paleoclim-CNRS/PaleoPisces)

Reference paper for the PISCES model is [Aumont et al. 2015](https://gmd.copernicus.org/articles/13/3011/2020/gmd-13-3011-2020.html). You will find additional information about parameterization and appropiate references within this paper as well.

This program aims to automate all the steps necessary to install and configure Paleo Pisces in order to facilitate the workflow.

It can be found on IRENE here:
```
/ccc/work/cont003/gen2212/gen2212/PaleoPisces
```

The folder structure is as following: 

![Folder Structure](/assets/images/Folder_paleo_pisces.png)

## Before starting

Before using the python scripts, once you are connected to IRENE make sure you use the right environment (Documentation [here](https://forge.ipsl.jussieu.fr/igcmg_doc/wiki/Doc/ComputingCenters/TGCC/Irene#Generalenvironment)).

Then load the python module v3.7.5:
```
ml load python3/3.7.5
```

Move in the folder containing the program:
```
cd /ccc/work/cont003/gen2212/gen2212/PaleoPisces
```

## Install Paleo Pisces

Install the model:
```
python3 install_paleo_pisces.py
```
You will be required to enter the folder name where you want to install Paleo Pisces (this folder shouldn't already exist).


This script will create 2 python files `sim_path.py` and `config_card.py` and put them in a folder located in `/ccc/work/cont003/gen2212/gen2212/PaleoPisces/user_input/[IRENE_USERNAME]`.


## Configure Paleo Pisces

Before running the next script you will have to edit the `sim_path.py` file which will contain path to the coupled simulation outputs.

![Sim path](/assets/images/sim_path.png)

Then the next python script can be run:
```
python3 configure_paleo_pisces.py
```
You will be required to enter the folder name where Paleo Pisces has been installed and the folder name where you want to set up the boundary conditions (this folder shouldn't already exist).


## Initialize Paleo Pisces

Before running the last script, you will need to edit the `config_card.py` python file which will contain settings for the `config.card` file:
```
vim /ccc/work/cont003/gen2212/gen2212/PaleoPisces/user_input/[YourUsername]/config_card.py
```
![Config card](/assets/images/config_card.png)

Finally you can run the last python script:
```
python3 initialize_paleo_pisces.py
```
You will be required to enter the folder name where Paleo Pisces has been installed and the folder name where the boundary conditions have been set up. Then you will have to choose the job specifications (project ID, type of node, number of cores and wall time).


## Run a simulation

If you wnat to run a simulation, you need to change your directory to:

```
cd ccc/work/cont003/gen2212/[IRENE_User_Name]/[path_to_PALEOPISCES]/modipsl/config/NEMO_v6/[JOB_NAME]
```

And run the job:
```
ccc_msub Job_[JOB_NAME]
```
