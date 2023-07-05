---
title: NCO tips
author: Anta
toc: true
toc_sticky: true
excerpt: List of useful tips and tricks for manipulating netcdf files on the command line
---

- __Change variables attributes__ 

_more complete documentation_ [here](https://linux.die.net/man/1/ncatted)

```
ncatted -a attribute_name,variable_name,m,f,value infile.nc
```

- __Rename variable or dimension__

_more complete documentation_ [here](https://linux.die.net/man/1/ncrename)

```
ncrename -v old_name,new_name infile.nc
ncrename -d old_name,new_name infile.nc
```

- __Drop variables from a file__

_more complete documentation_ [here](https://linux.die.net/man/1/ncks)

```
ncks -x -v variable1,variable2 infile.nc outfile.nc
```

- __Average files__

(useful in case you have output files averaged over 10Y and you want a 100Y average instead)

_more complete documentation_ [here](https://linux.die.net/man/1/ncea)

```
ncea File1.nc File2.nc Filex.nc Output.nc
```
