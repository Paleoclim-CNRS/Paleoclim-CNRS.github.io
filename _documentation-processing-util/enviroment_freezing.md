---
title: Environment Freezing
author: Wesley
toc: true
toc_sticky: true
excerpt: How to get all versions of installed python tools.
---

<div class='alert alert-info'>
This documentation refers to ModelServer however the concepts should be applicable to different environments.
</div>

# Introduction

To manage our environments we use a virtual environment manager called `conda` developed by [Anaconda](https://docs.conda.io/en/latest/). 
The benefits is that it is possible to install multiple distinct environments without them interfering with each other.

When distributing code it is important to also provide the execution environment to ensure the code will run on other systems. This is what this article aims to tackle.

# Activating the Environment

The first thing to do is to ensure you are in the correct working environment. To do this run:

```
conda activate python
```
<div class='alert alert-info'>
    <p>
    For ModelServer the <code class="language-plaintext">pangeo</code> environment in JupyterHub is called python.
    </p>
    <p>
    You can replace <code class="language-plaintext">python</code> with any other environment name. Run <code class="language-plaintext">conda env list</code> to see all installed environments.
    </p>
    <p>
    On super computers conda may not be available and this can be done with module for example.
    </p>
</div>

# Cloning

See [documentation](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#sharing-an-environment) for best explanation.

To clone the environment WITH EXACTLY the same package versions run 
```
conda env export > environment.yml
```

This will generate `environment.yml` you can then pass this to someone else and tell them to follow these [instructions](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#creating-an-environment-from-an-environment-yml-file).

In some cases a specific package version may no longer exist / be available (notably due to security issues in that version.) It is possible to also export ONLY the packages manually installed (the dependencies are installed automatically but are nto guaranteed to have the same version.) See [doc](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#exporting-an-environment-file-across-platforms)

```
conda env export --from-history > environment.yml
```

# Other useful tools:

## Conda list
You can see the installed versions and where the came from using:
```
conda list --explicit
```

You can also create a spec-file (used for creating environments `conda create --name myenv --file spec-file.txt`) like so:
```
conda list --explicit > spec-file.txt
```

## Pip Freeze

Pip is the standard Package Installer for Python (pip), when in the correct environment a simple:
```
pip freeze
```
or
```
pip freeze > requirements. txt
```

allows you to dump you current environment into a file that can be used as so `pip install -r requirements.txt`. HOWEVER this only plays well when everything is installed using pip (conda provides binaries which aren't reinstallable through pip but the opposite is true, conda can install pip wheels) and not conda so it is not advised.