---
author: Anthony
title:  Set up PALEO PISCES offline
excerpt: Installing and configuring Paleo Pisces offline
toc: true
---

[![](https://img.shields.io/static/v1?label=Code&message=here&color=lightgrey&style=flat-square&logo=github)](https://github.com/Paleoclim-CNRS/PaleoPisces)

Reference paper for the PISCES model is [Aumont et al. 2015](https://gmd.copernicus.org/articles/13/3011/2020/gmd-13-3011-2020.html). You will find additional information about parameterization and appropiate references within this paper as well.

This program aims to automate all the steps necessary to install and configure Paleo Pisces in order to facilitate the workflow.

The folder structure is as following: 

<p align="center">
    <img src="/assets/images/Folder_paleo_pisces.png"  width="800">
<p>

## Preliminary steps

The folder containing the script and all the packages required are located on IRENE here: `/ccc/work/cont003/gen2212/gen2212/PaleoPisces`.

Connect to IRENE and move to the folder location containing the code.
```
ssh [username]@irene-fr.ccc.cea.fr

cd /ccc/work/cont003/gen2212/gen2212/PaleoPisces
```

Load environment
```
. load_env.sh
```

## Install Paleo Pisces

Install the model:
```
python3 install_paleopisces.py
```
You will be required to enter the folder name where you want to install Paleo Pisces (this folder shouldn't already exist).

When launching for the first time this script, a `param.py` file will be created in `/ccc/work/cont003/gen2212/gen2212/PaleoPisces/user_input/[IRENE_USERNAME]`

<p align="center">
    <img src="/assets/images/param_paleo_pisces.png"  width="400">
<p>

<div class="alert alert-info">
<em>Note: This <code>param.py</code> file will request for element (folder/file/job name) to install and set up Paleo Pisces.

As long as one of these element entered in the <code>param.py</code> file doesn't match the conditions (either folder doesn't exist whereas it should for example), python script will request user to enter another value through a prompt to fix the issue.

If the new value fit the conditions it will be automatically updated in the <code>param.py</code> file at the concerned variable.</em>
</div>

## Configure Paleo Pisces

Before running the configure script you will have to edit the variables in `Folder names` and `Boundary condition files` section in `param.py` file.

Those variables will contain path to the coupled simulation outputs.

Then the next python script can be run:
```
python3 config_paleopisces.py
```

## Initialize Paleo Pisces

Before running the initialize script you will have to edit the variables in `Parameters for config.card` section in `param.py` file.

Finally you can run the last python script:
```
python3 init_paleopisces.py
```

## Run a simulation

If you want to run a simulation, you need to change your directory:

```
cd ccc/work/cont003/gen2212/[IRENE_User_Name]/[path_to_PALEOPISCES]/modipsl/config/NEMO_v6/[JOB_NAME]
```

And run the job:
```
ccc_msub Job_[JOB_NAME]
```
