---
author: Anthony
title: Diag CM5A2
excerpt: Use Diag_CM5A2.py file on IRENE to perform basic diags over CM5A2's outputs and create a pdf file that will contain the results
toc: true
---

<style>
    .initial-content div {border-radius: 10px; margin-bottom: 15px;}
    pre {background-color:#fafafa; border-radius: 7px;}
    code {background-color:#fafafa; color:#002d46; font-size:medium; border-radius: 4px;}
    .alert-warning code {background-color:#fafafa; color:#002d46; font-size:medium; padding: 2px 5px; border-radius: 4px;}
    div pre {margin:0px;}
</style>

[![](https://img.shields.io/static/v1?label=Code&message=here&color=lightgrey&style=flat-square&logo=github)](https://github.com/Paleoclim-CNRS/Diag_CM5A2/tree/IRENE_GEN2212)

## Introduction 
This script plots several diagnostics from CM5A2 outputs.
Depending on the options enabled:

- Figures format can be set (png, pdf...)
- PDF file gathering the generated figures can be created
- Animated gif can be created for some variables
- Manual value limits for plots colormap can be activated instead of the 
  automatic scaling for convenient comparison between several simulations
- In the case where 2 experiments are chosen, there is the ability to 
  compute the difference between them

## Preliminary steps

The folder containing the script and all the packages required are located on IRENE here: `/ccc/work/cont003/gen2212/gen2212/Diag_CM5A2/`.

Connect to IRENE and move to the folder location containing the code.
```
ssh [username]@irene-fr.ccc.cea.fr

cd /ccc/work/cont003/gen2212/gen2212/Diag_CM5A2/
```

At this step you can either choose to run `ccc_mprun -s -m work,store -Q test -p rome -T 600` or not before following the rest of the documentation. This will run the code on an **interactive computing node** (might be faster ?)

Load environment
```
. load_env.sh
```
<div class="alert alert-warning">Make sure to use <code>. load_env.sh</code> and not <code>./load_env.sh</code> </div>

## Launch the script

- Run the python script `Diag_CM5A2.py`
  ```
  python3 Diag_CM5A2.py
  ```

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
