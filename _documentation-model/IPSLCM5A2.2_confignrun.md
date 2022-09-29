---
title: IPSL-CM5A2 .2 and later versions
author: sebastienn
toc: true
toc_sticky: true

---

This is a documentation on the specific use of configuration 5A2.2 of IPSLCM (Coupled Model of IPSL).
It is an update to [Anta's documentation](https://paleoclim-cnrs.github.io/documentation-model/IPSL_config/).
You will find more details on how to run IPSLCM in general (any configuration) at TGCC and at IDRIS in the complete [IPSL-CMC documentation](https://forge.ipsl.jussieu.fr/igcmg_doc/wiki/Doc) on the supercomputers irene and jean-zay.

In particular you may have to set your [environment](https://forge.ipsl.jussieu.fr/igcmg_doc/wiki/Doc/ComputingCenters) before you can follow this tutorial.

# Download & Compile the code

The code is downloaded using the modIPSL tools:

```bash
# Create a working directory four your model installation

 cd $CCCWORKDIR

 mkdir MYPROJECTNAME

 cd MYPROJECTNAME

 svn co http://forge.ipsl.jussieu.fr/igcmg/svn/modipsl/trunk modipsl
 
 cd modipsl/util

# List available models

 ./model -h

# Retreive model IPSLCM5A2.2 or later

./model IPSLCM5A2.2

```
When prompted for a password for IPSLCM experiments/libIGCM use: 

```bash
# login
icmc_users
# password
icmc2022
```
When prompted for a password for Orchidee use:

```bash
# login
sechiba
#password
ipsl2000
```

Once this is done you can immediately compile the model for a paleo experiment.
Starting from v(.2) There is no need to modify the sources for configurations without ice sheets.

Launch the Makefile script for PALEOIPSLCM5A2-VLR configuration:

```bash
gmake PALEOIPSLCM5A2-VLR
```
