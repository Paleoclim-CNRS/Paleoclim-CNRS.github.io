---
title: Visu CM5A2
author: Anthony
toc: true
toc_sticky: true
excerpt: Visualization script for CM5A2 outputs
---
<style>
    .initial-content div {border-radius: 10px; margin-bottom: 15px;}
    pre {background-color:#fafafa; border-radius: 7px;}
    code {background-color:#fafafa; color:#002d46; font-size:medium; padding: 2px 0px; border-radius: 4px;}
    img {border-radius: 10px;}
    div pre {margin:0px;}
    li {margin-left:30px;}

    .general {background-color:#f9f9f9; padding: 15px; color:#002d46;}
    .error {background-color:#ff4816; color:#ffffff; padding: 20px; border-radius: 7px;}
    .light-info {background-color:#cfd9d9; color:#002d46; border-radius: 7px; padding: 20px;}
    .section {background-color:#4b78a1;padding: 15px;}
    .sub-section {background-color:#b9bdbd;padding: 3px 0px;}

    .alert-info {background-color:#7bb7db; color:#002d46; padding: 20px}
    .alert-warning {background-color:#f3c778; color:#002d46; padding: 20px}
    .alert-success { padding: 20px}

    .alert-warning code, .alert-info code, .light-info code, .alert-success code, .error code {padding:0px 5px;}
    .alert-info a {color:#245b9c}
    .alert-info a:hover {color:#1c4576}
    .alert-info a:active {color:#472197}
    .light-info a {color:#0d4268}
    .light-info a:hover {color:#13629b}
    .light-info a:active {color:#472197}
    .alert-warning a {color:#815518}
    .alert-warning a:hover {color:#37240a}
    .alert-warning a:active {color:#472197}
</style>

These scripts allow to visualize CM5A2 outputs. Plot are customizable with the ability to use custom colormap, add contour lines, etc... For more details you can check the header of each script which will explain all the inputs necessary (their format, specificities, etc...).

They are available on IRENE: `/ccc/work/cont003/gen2212/gen2212/util_CM5A2/visu_CM5A2/`

They can be run on IRENE or on local machin. 

If run on IRENE, don't forget to load the right environment:
```
. load_env.sh
```

If run on a local machine, they should work with:
```
python     3.8
hdf5       1.10.6
netcdf4    1.6.2 
numpy      1.21.5
xarray     0.20.1
matplotlib 3.5.2
cartopy    0.18.0
``````

# horizontal_section_plot.py
Plot horizontal section (lon/lat plot) of variable from CM5A2 output file (`grid_T`, `ptrc_T`, `diad_T`, ...). Variable can be plotted for specific time step or time averaged over specific period and at specific depth or depth integrated over specific depths range.

<p align="center">
    <img src='/assets/images/documentation/Visu_CM5A2/horizontal_section_plot.png' style="width:300px;">
</p>

# horizontal_section_current_arrow_plot.py
Same as `horizontal_section_plot.py` but adds current arrows (using `grid_U` and `grid_V` files) on top of plot.

<p align="center">
    <img src='/assets/images/documentation/Visu_CM5A2/horizontal_section_current_arrow_plot.png' style="width:300px;">
</p>

# horizontal_section_plot_seasonnal.py
Same as `horizontal_section_plot.py` but creates 4 plots.
The first 3 plots are averages: annual, winter(jan-mar), summer (jul-sep).
The 4th plot is difference: winter-summer.

<p align="center">
    <img src='/assets/images/documentation/Visu_CM5A2/horizontal_section_plot_seasonnal.png' style="width:300px;">
</p>

# vertical_section_plot.py
Plot vertical section (lon or lat /depth plot) of variable from CM5A2 output file (`grid_T`, `ptrc_T`, `diad_T`, ...). Variable can be plotted for specific time step or time averaged over specific period and at specific lon/lat slice or averaged over specific lon/lat ranges. Subbasin  mask can be applied.

<p align="center">
    <img src='/assets/images/documentation/Visu_CM5A2/vertical_section_plot.png' style="width:300px;">
</p>

# O2_bottom_plot.py
Gather `O2` variable from `ptrc_T` file at deepest cell over the domain and plot it.

<p align="center">
    <img src='/assets/images/documentation/Visu_CM5A2/O2_bottom_plot.png' style="width:300px;">
</p>

# bathy_plot.py
PLots bathymetry computed from `thetao` and `deptht` variables in `grid_T` file. 
Then plots histogram showing sum of cells surface ordonned by depth bins (500m reange each) for each subbasin (atlantic, pacific, indian).

<p align="center">
    <img src='/assets/images/documentation/Visu_CM5A2/bathy_plot1.png' style="width:300px;">
</p>
<p align="center">
    <img src='/assets/images/documentation/Visu_CM5A2/bathy_plot2.png' style="width:300px;">
</p>
<p align="center">
    <img src='/assets/images/documentation/Visu_CM5A2/bathy_plot3.png' style="width:300px;">
</p>

# O2_Compute.py
Compute saturated O2 from `O2` variable in `ptrc_T` file. The three variables created (`oxygen_4`, `O2_SAT4`, `AOU4`) can then be visualized with `horizontal_section_plot.py` or `vertical_section_plot.py` scripts.
