---
title: Working on IRENE
author: Anthony
toc: true
toc_sticky: true
excerpt: Some good practices to know when working on IRENE
---

<style>
    .initial-content div {border-radius: 10px; margin-bottom: 15px;}
    pre {background-color:#fafafa; border-radius: 7px;}
    code {background-color:#fafafa; color:#002d46; font-size:medium; padding: 2px 0px; border-radius: 4px;}
    img {border-radius: 10px;}
    div pre {margin:0px;}

    .general {background-color:#f9f9f9; padding: 15px; color:#002d46;}
    .error {background-color:#ff4816; color:#ffffff; padding: 10px; border-radius: 7px;}
    .light-info {background-color:#cacece; color:#002d46; border-radius: 7px; padding: 10px;}
    .section {background-color:#4b78a1;padding: 15px;}
    .sub-section {background-color:#b9bdbd;padding: 3px 0px;}

    .alert-info {background-color:#7bb7db; color:#002d46; padding: 10px}
    .alert-warning {background-color:#f3c778; color:#002d46; padding: 10px}
    .alert-success { padding: 10px; color:#002d46}

    .alert-warning code, .alert-info code, .light-info code, .alert-success code, .error code {padding:0px 5px}
    .alert-info pre, .alert-info code {background-color:#dcf5ff}
    .alert-success pre, .alert-success code {background-color:#d6fff0}
    .alert-info a {color:#245b9c}
    .alert-info a:hover {color:#1c4576}
    .alert-info a:active {color:#472197}
    .alert-warning a {color:#815518}
    .alert-warning a:hover {color:#37240a}
    .alert-warning a:active {color:#472197}
</style>

This guide gather some useful tips and good practices to get used to when working on IRENE as well as some solving concerning issues which can be encountered.

This is not a comprehensive documentation on the **TGCC** computing center (which can be found [here](https://www-hpc.cea.fr/tgcc-public/en/html/toc/fulldoc/Introduction.html)).

# Module management

To list all the modules installed on your environnment, you can use:
```
module list
```

If you need to load specific module on your environment, you can check the different versions available by using:
```
module avail [MODULE]
```

With your module, some dependencies might be required to be loaded also so it can work properly. To check this, use:
```
module help [MODULE]
```
A list of commands of the requested module and its dependencies to load will be displayed.

Before using one the displayed commands, it is advised to clean your environment by unloading all the installed modules in order to avoid any conflicts between them:
```
module purge
```

Then you can load your module(s):
```
module load [MODULE]
#OR
module load [MODULE1] [MODULE2] ...
```


Exemple:
```
module avail python3
module help python3/3.10.6
module purge
module load intel/20 mpi/openmpi/4 python3/3.10.6
```

If you want to remove a specific module from your environnment, use:
```
module unload [MODULE]
```

Note: `ml` is a shortcut for `module` (`ml avail`/`ml help`/`ml load` ...).


# Folders permissions and group
## Permissions
All the folders in your user space on IRENE should have the `s` permission for group (exemple: `drwxr-s---`. The `s` replacing the `x`).

If it is not the case you can add this right to a `folder` by using:
```
chmod g+s folder
```

## Group
When running command in one of your folders:
```
ls -lrt
```
You can see the owner and the group of each file/folder, exemple:
```
[USER@irene193 ~]$ ls -lrt
total 50848
-rwxr--r--. 1 USER GROUP 47080376 Nov 30 15:53 FILE_1
-rw-r--r--. 1 USER GROUP  3446468 Dec  3 15:49 FILE_2
drwxr-sr-x. 2 USER GROUP     4096 Dec  1 19:41 FOLDER_1
-rwxr--r--. 1 USER GROUP   759732 Nov 30 15:53 FILE_3
drwxr-s---. 2 USER GROUP     4096 Dec  3 16:15 FOLDER_2
drwxr-xr-x. 3 USER GROUP     4096 Dec  3 15:40 FOLDER_3
```

The `USER` should be your username on IRENE and the `GROUP` can be either the organisation group (`cerege`) or the project group (`gen2212`).

To avoid any issue, your files/folders on IRENE **should always be owned** by the `gen2212` group **and not** the `cerege` one.

You can change this by using the following command line:
```
chown -R USER:GROUP [folder]/[file]
```

In our case to change the group to `gen2212`:
```
chown -R IRENE_USERNAME:gen2212 [folder]/[file]
```

## Copy data
When copying data from an external source to IRENE it is good practice to use `scp` with specific options:
```
scp -r LOCAL_PATH/ IRENE_USERNAME@irene-fr.ccc.cea.fr:IRENE_PATH/`
```

(Do not use the -p option if the reception folder on IRENE has the `s` right on group)

You can also use `rsync`:
```
rsync --chmod=Dg+s --chown=:gen2212 LOCAL_PATH/ IRENE_USERNAME@irene-fr.ccc.cea.fr:IRENE_PATH/
```

## Troubleshooting

- `Disk quota exceeded` error

    Explanation:

    All the generated files/folders (either by creating them or by copying them from an external source) in a folder which hasn't the `s` permission for the group will be associated to the organisation group (organisation group = `cerege` for us) which has a very limited space and which will eventually lead to this disk quota issue.

    If you  get this error, refer to the sections [Permissions](#permissions), [Group](#group) and [Copy data](#copy-data) to deal with it.

# Submit Jobs and execute srcipts
Comprehensive documentation can be found on the official TGCC website [here](https://www-hpc.cea.fr/tgcc-public/en/html/toc/fulldoc/Job_submission.html).

Remember you can get information about submitted jobs on the supercomputer with command [ccc_mpp](https://www-hpc.cea.fr/tgcc-public/en/html/toc/fulldoc/Job_submission.html#ccc-mpp).

## Submit a job
A job is a submission script which will contain informations on how to run a script on computation nodes.

Here is an exemple of a job file to run a python script:
```
#!/bin/bash
#MSUB -r Job_Diag                  # Request name
#MSUB -n 1                         # Total number of tasks to use
#MSUB -m work
#MSUB -Q test
#MSUB -T 1800                      # Elapsed time limit in seconds
#MSUB -e err_%J.err                # Standard output. %I is the job id
#MSUB -o out_%J.out                # Error output. %I is the job id
#MSUB -q rome                      # Choosing partition of GPU nodes
#MSUB -A gen2212                   # Project ID
set -x

ml purge
ml sw dfldatadir/gen2212
module load gnu/11 mpi/openmpi/4 python3/3.8.10
ccc_mprun ./Diag_CM5A2.py
```

A job can be launched with command `ccc_msub [job_file]`


## Launch a script
You can also run a script straight away with the command [ccc_mprun](https://www-hpc.cea.fr/tgcc-public/en/html/toc/fulldoc/Job_submission.html#ccc-mprun). It will however require some option to run the script properly on a computation node, for example:
```
ccc_mprun -n 1 -m work -Q test -p rome -T 600 ./Diag_CM5A2.py
```

<div class="alert alert-info">
Make sure you allow access to the right space (<em>#WORK</em>, <em>#STORE</em>, <em>#SCRATCH</em>) when using this command. For example:
<li>If your script is on the <em>#WORK</em>, use <code>-m work</code></li>
<li>If your script is on the <em>#WORK</em> and use inputs or write outputs on the <em>#STORE</em> use <code>-m work,store</code></li>
</div>

## Launch a script in interactive mode 

This is useful script which require manual entries.

If the script requires manual keyboard entry, you will not be able to use it with a [submission script](#submit-a-job) nor from the frontal with command [ccc_mprun](#submit-a-script).

You need first to move to an interactive computation node. You can do this with the command `ccc_mprun` and the option `-s`. Here is a n example:
```
ccc_mprun -s -m work -Q test -p rome -T 600
```

Once you are on the node, you can launch your script as if you were on your local machine `python3 script.py`, `sh script.sh`, ...

Command details:
```
ccc_mprun -s (interactive) -m work (access to the #WORK) -Q test (type of queue) -p rome (partition corresponding to your computation hours) -T 600 (time limit)
```

As previous section [Launch a script](#launch-a-script), make sure you allow access the right spaces depending on your script and its inputs/outputs location.

# Other useful commands

Quotas
```
ccc_quota              # my personal quota
ccc_quota -g gen2212.  # gen2212 group quota
ccc_quota -a           # all quotas
```
