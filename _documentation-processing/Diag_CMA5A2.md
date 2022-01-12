---
author: Anthony
title: Diag CMA5A2
excerpt: Use Diag_CMA5A2.py file on IRENE to perform basic diags over CMA5A2's outputs and create a pdf file that will contain the results
toc: true
---

The folder containing the script and all the packages required is located on IRENE here: `/ccc/store/cont003/gen2212/gen2212/Diag_CMA5A2/`.

This script plots several diagnostics from CMA5A2 outputs.
Depending on the options enabled:

- Figures format can be set (png, pdf...)
- PDF file gathering the generated figures can be created
- Animated gif can be created for some variables
- Manual value limits for plots colormap can be activated instead of the 
  automatic scaling for convenient comparison between several simulations
- In the case where 2 experiments are chosen, there is the ability to 
  compute the difference between them



## Launch the script

- Launch the script by running the `RUN_DIAG.sh` file (command `sh RUN_DIAG.sh`)

- Enter your `[ProfileName]` as requested:
    - if `[ProfileName]` is not known: a file `[ProfileName]_profile.py` will be created here `userData/[ProfileName]/` and the program will stop. 
    At this point, you will need to edit the `[ProfileName]_profile.py` file  according to your needs and relaunch the program.
    An example can be found in `userData/EXAMPLE/EXAMPLE_profile.py`.
    - if `[ProfileName]` is known: program will run following the settings of the `userData/[ProfileName]/[ProfileName]_profile.py` file.

    Note that the variables in `[ProfileName]_profile.py` file:
    - `sentence_to_use`
    - `dataDirOutput`
    - `filePrefix`
    - `dataDirMask`
    - `maskFile`
    - `dataDirBathy`
    - `bathyFile`
    - `subBasinFile`
    
    are in list format and can contain as many elements as you want (elements being the different properties of the experiments: file names, paths...).
    
    In the specific case where you choose 2 experiments, a new prompt will appear letting you choose the ability to compute differences between experiments 1 & 2.

Note: In order to run, this script will need the `util/` and `Toolz/` folders containing all the necessary functions and templates.
