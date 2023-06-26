---
title: Launch a CM5A2.2 simulation (from scratch)
author: Anthony
toc: true
toc_sticky: true
excerpt: How to prepare and launch a coupled simulation
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
    .alert-success { padding: 10px}

    .alert-warning code, .alert-info code, .light-info code, .alert-success code, .error code {padding:0px 5px;}
    .alert-info a {color:#245b9c}
    .alert-info a:hover {color:#1c4576}
    .alert-info a:active {color:#472197}
    .alert-warning a {color:#815518}
    .alert-warning a:hover {color:#37240a}
    .alert-warning a:active {color:#472197}
</style>

<div class="error">This is a preliminary work. This guide will be subject to change soon.</div> 

<div class="alert general">
  Directories in which you should be to run the commands are given in grey squares:
  <div class="alert light-info">
    Location:
    <pre><code>PATH/WHERE/YOU/MUST/MOVE/TO/</code></pre>
  </div>
  General notes/tips are specified in blue squares:
  <div class="alert alert-info">
    This is a note or a tip
  </div>
  Warnings or specific cases where you can have several options are indicated in yellow squares:
  <div class="alert alert-warning">
    This is a warning or a specific case
  </div> 
</div>

# Preliminary steps

<div class="alert alert-info">
  Throughout this guide, keep in mind all the elements between brackets are variables names that you will have to adapt to your case (e.g. <code>[IRENE_USER]</code>, <code>[CPL-SIM]</code>, etc... )
</div>

## Get the right environment on IRENE

Make sure you have set up the **igcmg environment** on your **IRENE** user space ([Documentation](https://forge.ipsl.jussieu.fr/igcmg_doc/wiki/Doc/ComputingCenters/TGCC)).

Since **redhat8** update on **IRENE**, you can find complementary elements on modifications you need to perform [here](https://forge.ipsl.jussieu.fr/igcmg_doc/wiki/Doc/ComputingCenters/TGCC/IreneRedHat8).

More information can be found on general [IPSL-CMC documentation page](https://forge.ipsl.jussieu.fr/igcmg_doc/wiki/Doc).

## Download and compile model

Follow the Sébastien's [Guide](https://paleoclim-cnrs.github.io/documentation-model/IPSLCM5A2.2_confignrun/)

<div class="alert alert-info">
  In the following documentation, model installation folder is called <b>CM5A2.2</b>
</div>

## Prerequisite files
Depending on the simulation you want to launch, you will need at least 2 input files that you can't generate yourself (???)

- `topo_bathy_1x1_file.nc` needed for **Netcdf_Editor App**
- `coordinate_file.nc` to provide in **opa9.card** for coupled simulation

<div class="alert alert-info">
  Note these 2 files have to be consistent between each other. Singular points from <code>coordinate_file.nc</code> have to be located over land area defined by <code>topo_bathy_1x1_file.nc</code>.
</div>

## Generate initial/boundary condition files

For this let's create a folder on IRENE which will contain all the files produced in this section.

Connect to IRENE:
```
ssh [IRENE_USER]@irene-fr.ccc.cea.fr
```

<div class="alert light-info">
  Location:
  <pre><code>$CCCWORKDIR</code></pre>
</div>

Create the folder:
```
mkdir BC_IPSLCM5A2
```


### Initial condition files

<div class="alert light-info">
  Location:
  <pre><code>/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/CI_temp_salin/</code></pre>
</div>

Read instructions in `readme` to execute `CI_temp_salin.py` script. It will generate outputs in path specified by `path_out` variable in `user_input/[IRENE_USER]_path.py` file

For this guide, we will assume the variable `path_out` has been defined to `$CCCWORKDIR/BC_IPSLCM5A2/OUT_CI_temp_salin/`

<div class="alert light-info">
  Location:
  <pre><code>/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/</code></pre>
</div>

Then use `CreateRestartOce4Oasis.bash` script (source `/ccc/work/cont003/gen2212/p519don/BC_PALEOIPSL/COUPLER/EXE/`).

Below is a table resuming all the files you will need and how to generate them:

File to create|Generated with|Input file needed|To provide in|Sim type
-|:-:|:-:|:-:|:-:
`amipbc_sst_1x1.nc`|`CI_temp_salin.py`|Random `amipbc_sst_1x1.nc`|*lmdz.card*|*LMDZ*
`data_1m_potential_temperature_nomask.nc`|`CI_temp_salin.py`|Random `data_1m_potential_temperature_nomask.nc`|*opa9.card*|*COUPLED*
`data_1m_salinity_nomask.nc`|`CI_temp_salin.py`|Random `data_1m_salinity_nomask.nc`|*opa9.card*|*COUPLED*
`sst_data.nc`|`CI_temp_salin.py`|Random `sst_data.nc`|*opa9.card*|*COUPLED*
`sss_data.nc`|`CI_temp_salin.py`|Random `sss_data.nc`|*opa9.card*|*COUPLED*
`sstoc.nc`|`CreateRestartOce4Oasis.bash`|`amipbc_sst_1x1.nc` produced by `CI_temp_salin.py`|*oasis.card*|*COUPLED*

### Boundary condition files
To generate Boundary condition files, use the [Netcdf Editor App](https://climate_sim.osupytheas.fr) ([doc](https://paleoclim-cnrs.github.io/netcdf_editor_app/multi)).

The output will be generated in a folder <code>[OUT_APP]</code> (named after the input file you provided) organized as follow:
```
[OUT_APP]/
  [OUT_APP]_ahmcoef_netcdf_flask_app.nc
  [OUT_APP]_bathy_netcdf_flask_app.nc
  [OUT_APP]_coords_mask_netcdf_flask_app.nc
  [OUT_APP]_coords_netcdf_flask_app.nc
  [OUT_APP]_heatflow_netcdf_flask_app.nc
  [OUT_APP]_pft_netcdf_flask_app.nc
  [OUT_APP]_raw_netcdf_flask_app.nc
  [OUT_APP]_routing_netcdf_flask_app.nc
  [OUT_APP]_soils_netcdf_flask_app.nc
  [OUT_APP]_sub_basins_netcdf_flask_app.nc
  [OUT_APP]_topo_high_res_netcdf_flask_app.nc
  [OUT_APP]_weights_netcdf_flask_app.tar.gz
  mosaix_weights/
    areas_ORCA2.3xLMD9695_v2.nc
    dia_tLMD9695_to_tORCA2.3_HeatWaterFluxes_1stOrder_v2.nc
    dia_tLMD9695_to_tORCA2.3_HeatWaterFluxes_2ndOrder_v2.nc
    ...
```

Once you have downloaded the produced files by the app on your local computer:

Create a folder `BC_IPSLCM5A2` on your workdir on IRENE:
```
ssh [IRENE_USER]@irene-fr.ccc.cea.fr
cd $CCCWORKDIR/
mkdir BC_IPSLCM5A2
```

And copy data to it (command to execute from your local computer):
```
rsync -aruv --chmod=Dg+s --chown=:gen2212 /[PATH_TO]/[OUT_APP] irene-fr.ccc.cea.fr:/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2
```

# Prepare a coupled simulation from scratch

In order to launch a coupled simulation, atmosphere and vegetation models need to be initialized and at equilibrium first.

- **LMDZ** (Atmosheric component) has to be run for 1 year
- **LMDZOR** (Atmospheric component coupled with vegetation component) has to be run for 1 year
- **Coupled** system can be launched

## LMDZ

<div class="sub-section"></div>

**config.card**

<div class="alert light-info">
  Location:
  <pre><code>$CCCWORKDIR/CM5A2.2/modipsl/config/IPSLCM5A2/</code></pre>
</div>

Copy it from base config:
```
cp EXPERIMENTS/LMDZ/CREATE_clim/config.card .
```

Modify it:
```
vim config.card
```
```
JobName=[ELC-SIM]
DateBegin=1979-01-01
DateEnd=1979-12-30
```

<div class='error'></div>

And add after l.15 `ExpType=LMDZ/CREATE_clim`: 
```
#============================                                                   
#-- Source following file with module settings, only if it exists
EnvFile=${SUBMIT_DIR}/../ARCH/arch.env
```
<div class='error'></div>

<div class='alert alert-info'>
  <li><code>JobName</code> can only contain alphanumeric characters, "." and "-"</li>
  <li><code>JobName</code> must start with a letter</li>
  <li><code>JobName</code> must contain 29 letters maximum</li>
</div>


<div class="sub-section"></div>

**Job**

Execute the script `ins_job`:
```
../../libIGCM/ins_job
```

<div class='alert alert-info'>

  Just keep default configuration (press "enter") for the 4 requests (<b>ProjectID</b>, <b>ProjectNode</b>, <b>ProjectCore</b> and <b>Wall time limit</b>)
</div>

<div class="sub-section"></div>

**lmdz.card**

<div class="alert light-info">
  Location:
  <pre><code>$CCCWORKDIR/CM5A2.2/modipsl/config/IPSLCM5A2/[ELC-SIM]/</code></pre>
</div>

Modify it:
```
vim COMP/lmdz.card
```
```
LMDZ_Physics=AP

...

[BoundaryFiles]
List= ()

ListNonDel= (${R_IN}/ATM/Albedo.nc                                                                                            , .                 ), \
            (${R_IN}/ATM/ECDYN.nc.20020101                                                                                    , ECDYN.nc          ), \
            (${R_IN}/ATM/ECDYN.nc.20020101                                                                                    , ECPHY.nc          ), \
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/[OUT_APP]_topo_high_res_netcdf_flask_app.nc        , Relief.nc         ), \
            (${R_IN}/ATM/Rugos.nc                                                                                             , .                 ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/ice/landiceref_NOICE.nc                          , landiceref.nc     ), \
            (${R_IN}/ATM/Ozone/HYBRIDE/v2.clim/tro3_1855.new.nc                                                               , climoz.nc         ), \
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/OUT_CI_temp_salin/[file_sst_bc_clim.nc]                      , amipbc_sst_1x1.nc ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/ice/no_sic_bc_clim.nc                            , amipbc_sic_1x1.nc ), \
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/mosaix_weights/LMD9695_grid_maskFrom_ORCA2.3_v2.nc , o2a.nc            )

[ParametersFiles]
List= ...
      (${SUBMIT_DIR}/PARAM/config.def_preind     ,  config.def),    \
      ...

```

- `amipbc_sst_1x1.nc` is produced from `CI_temp_salin.py` script (see [Initial condition files](#initial-condition-files))
- `Relief.nc` and `o2a.nc` are produced from the **Netcdf Editor App** (see [Boundary condition files](#boundary-condition-files))

<div class="alert alert-info">
      For <code>amipbc_sst_1x1.nc</code> : this file should be consistent with the <code>sstoc.nc</code> file you will provide in the <b>oasis.card</b> from the coupled simulation you will launch hereafter.
</div>

<div class="alert alert-warning">
  <b>TODO</b><br>
  Add ability to implement <b>land ice</b> and <b>sea ice</b>, in this case there will be 2 choices:<br>
  <li>Without ice (<code>landiceref_NOICE.nc</code>, <code>no_sic_bc_clim.nc</code>)</li>
  <li>With ice (use scripts to generate file...)</li>
</div>

<div class="sub-section"></div>

**config.def_preind**

In this file you can change CO2 and other greenhouse gases rates as well as orbital parameters. 

Modify it:
```
vim PARAM/config.def_preind
```

<div class="sub-section"></div>

**Simulation**

Launch the simulation
```
ccc_msub Job_[ELC-SIM]
```

<div class="sub-section"></div>

## LMDZOR

<div class="sub-section"></div>

**config.card**

<div class="alert light-info">
  Location:
  <pre><code>$CCCWORKDIR/CM5A2.2/modipsl/config/IPSLCM5A2/</code></pre>
</div>

Copy it from base config:
```
cp EXPERIMENTS/LMDZOR/paleo/config.card .
```

Modify it:
```
vim config.card
```
```
JobName=[LMDZOR-SIM]
DateBegin=0001-01-01
DateEnd=0001-12-30

...

#D-- Executable -
[Executable]
#D- For each component: Real name of executable, Name of executable in the run directory
ATM= (gcm.e, lmdz.x, 47MPI, 8OMP)
```
<div class="alert alert-info">
  Those modifications in <b>Executable</b> section allocate an optimized numbers of proc to run the 1 year LMDZOR faster
</div>

<div class='alert alert-info'>
  <li><code>JobName</code> can only contain alphanumeric characters, "." and "-"</li>
  <li><code>JobName</code> must start with a letter</li>
  <li><code>JobName</code> must contain 29 letters maximum</li>
</div>

<div class="sub-section"></div>

**Job**

Execute script ins_job: 
```
../../libIGCM/ins_job
```

<div class='alert alert-info'>

  Just keep default configuration (press "enter") for the 4 requests (<b>ProjectID</b>, <b>ProjectNode</b>, <b>ProjectCore</b> and <b>Wall time limit</b>)
</div>

<div class="sub-section"></div>

**lmdz.card**

<div class="alert light-info">
  Location:
  <pre><code>$CCCWORKDIR/CM5A2.2/modipsl/config/IPSLCM5A2/[LMDZOR-SIM]/</code></pre>
</div>

Modify it:
```
vim COMP/lmdz.card
```
```
CREATE=[ELC-SIM]
```

<div class="sub-section"></div>

**orchidee.card**

Modify it by using files produced by the Netcdf App (see [Generate boundary condition files](#generate-boundary-condition-files)):

```
vim COMP/orchidee.card
```
```
[InitialStateFiles]
List=   (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/[OUT_APP]_soils_netcdf_flask_app.nc   , soils_param.nc ), \
        (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/[OUT_APP]_routing_netcdf_flask_app.nc , routing.nc     ), \
        (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/[OUT_APP]_pft_netcdf_flask_app.nc     , PFTmap.nc      )
```

<div class="sub-section"></div>

**config.def_preind**

Set this file as the one from your ELC simulation or copy it from.

```
cp ../[ELC-SIM]/PARAM/config.def_preind PARAM/
```

<div class="sub-section"></div>

**Simulation**

Modify `Job-[LMDZOR-SIM]` :
```
vim Job-[LMDZOR-SIM]
```
```
#MSUB -n 377 # Number of MPI tasks (SPMD case) or cores (MPMD case)
```

Launch the simulation:
```
ccc_msub [LMDZOR-SIM]
```

<div class="sub-section"></div>

## COUPLED

<div class="alert alert-info">
  You can find reference simulations here if you have any question on file formating, values, etc... 
  <br>
  <pre><code>/ccc/work/cont003/gen2212/p519don/IPSL-PALEO/modipsl/config/IPSLCM5A2/</code></pre>
</div>

### Oasis coupler

<div class="alert light-info">
  Location:
  <pre><code>$CCCWORKDIR/BC_IPSLCM5A2/</code></pre>
</div>

The `BC_IPSLCM5A2` should have already been created - see [Generate boundary condition files](#generate-boundary-condition-files)

Download the `CreateRestartAtm4Oasis.bash` script:
```
svn co  http://forge.ipsl.jussieu.fr/igcmg/svn/TOOLS/CPLRESTART
```

A folder `CPLRESTART` containing the `CreateRestartAtm4Oasis.bash`/`CreateRestartOce4Oasis.bash` as well as other necessary python scripts will be downloaded.

<div class="alert light-info">
  Location:
  <pre><code>$CCCWORKDIR/BC_IPSLCM5A2/CPLRESTART</code></pre>
</div>

Copy `histmth.nc` file generated by LMDZOR 1 year run:
```
cp $CCCSTOREDIR/IGCM_OUT/LMDZOR/PROD/clim/[LMDZOR-SIM]/ATM/Output/MO/[LMDZOR-SIM]_[DATE]_1M_histmth.nc .
```

Extract last month of this file:
```
ncks -d time_counter,-1,-1 [LMDZOR-SIM]_[DATE]_1M_histmth.nc [LMDZOR-SIM]_[DATE]_1M_histmth_last_month.nc
```

Run `CreateRestartAtm4Oasis.bash`:
```
bash ./CreateRestartAtm4Oasis.bash [LMDZOR-SIM]_[DATE]_1M_histmth_last_month.nc
```

Store results in a new dir called after your coupled simulation name:
```
mkdir [CPL-SIM]
mv add_dim.nco create_flxat.nco flxat_LMD9695_maskFrom_ORCA2.3.nc icbrg_LMD9695_maskFrom_ORCA2.3.nc icshf_LMD9695_maskFrom_ORCA2.3.nc lonlat2xyz.nco lonlat2xyz.nco [LMDZOR-SIM]_[DATE]_1M_histmth.nc [LMDZOR-SIM]_[DATE]_1M_histmth_last_month.nc [CPL-SIM]
```

### Param files

<div class="sub-section"></div>

**config.card**

<div class="alert light-info">
  Location:
  <pre><code>$CCCWORKDIR/CM5A2.2/modipsl/config/IPSLCM5A2/</code></pre>
</div>

Copy it from base config:
```
cp EXPERIMENTS/IPSLCM/piControl/config.card .
```

Modify it:
```
vim config.card
```
```
JobName= [CPL-SIM]
ExperimentName=paleo
SpaceName=PROD
ExpType=IPSLCM/paleo
CalendarType=360d
DateBegin=0001-01-01
DateEnd=0100-12-30
...
#========================================================================
#D-- Restarts -
[Restarts]
OverRule=n

...
#========================================================================
#D-- ATM -
[ATM]                                                                       
WriteFrequency="1M"                                                         
Restart= y                                                                  
RestartDate=0001-12-30                                                      
RestartJobName=[LMDZOR-SIM]                                       
RestartPath=/ccc/store/cont003/gen2212/[IRENE_USER]/IGCM_OUT/LMDZOR/PROD/clim/

...
#========================================================================
#D-- OCE -
[OCE]
WriteFrequency="1M 1Y"

...
#========================================================================     
#D-- SRF -                                                                  
[SRF]                                                                       
WriteFrequency="1M"                                                         
Restart= y                                                                  
RestartDate=0001-12-30                                                      
RestartJobName=[LMDZOR-SIM]                                       
RestartPath=/ccc/store/cont003/gen2212/[IRENE_USER]/IGCM_OUT/LMDZOR/PROD/clim      
#========================================================================
#D-- SBG - STOMATE                                                          
[SBG]                                                                       
WriteFrequency="1M"                                                         
Restart= y                                                                  
RestartDate=0001-12-30                                                      
RestartJobName=[LMDZOR-SIM]                                       
RestartPath=/ccc/store/cont003/gen2212/gramoula/IGCM_OUT/LMDZOR/PROD/clim

...
#========================================================================      
#D-- Executable -                                                            
[Executable]
#D- For each component, Real name of executable, Name of executable for oasis                                                                 
ATM= (gcm.e, lmdz.x, 47MPI, 8OMP)
SRF= ("" ,"" )
SBG= ("" ,"" )
OCE= (opa, opa.xx, 100MPI)

...
#========================================================================         
#D-- Post -                                                                       
[Post]
...
#D- If you want to produce time series, this flag determines
#D- frequency of post-processing submission (NONE if you don't want)                                                                                
TimeSeriesFrequency=50Y
#D- If you want to produce seasonal average, this flag determines
#D- the period of this average (NONE if you don't want)
SeasonalFrequency=100Y                                                            
```

<div class='alert alert-info'>
  <li><code>JobName</code> can only contain alphanumeric characters, "." and "-"</li>
  <li><code>JobName</code> must start with a letter</li>
  <li><code>JobName</code> must contain 29 letters maximum</li>
</div>

<div class='alert alert-info'>
  In this example we have defined a 100 years run, if you want to change it modify <code>DateEnd</code> variable consequently
</div>

<div class="sub-section"></div>

**Job**

Execute script `ins_job`:
```
../../libIGCM/ins_job
```

<div class='alert alert-info'>

  Just keep default configuration (just press "enter") for the 3 first requests (<b>ProjectID</b>, <b>ProjectNode</b> and <b>ProjectCore</b>) and set <b>Wall time limit</b> to <code>86400</code>. <br> You will also be able to modify this value afterward in the <code>Job_[CPL-SIM]</code> (e.g. <code>#MSUB -T 86400</code>).
</div>

<div class="sub-section"></div>

**lmdz.card**

<div class="alert light-info">
  Location:
  <pre><code>$CCCWORKDIR/CM5A2.2/modipsl/config/IPSLCM5A2/[CPL-SIM]/</code></pre>
</div>

*NO MODIFICATIONS* for `COMP/lmdz.card`

<div class="sub-section"></div>

**orchidee.card**

Modify it by using files produced by the Netcdf App (see [Generate boundary condition files](#generate-boundary-condition-files)):
```
vim COMP/orchidee.card
```
```
[InitialStateFiles]
List=   (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/[OUT_APP]_soils_netcdf_flask_app.nc   , soils_param.nc ), \
        (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/[OUT_APP]_routing_netcdf_flask_app.nc , routing.nc     ), \
        (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/[OUT_APP]_pft_netcdf_flask_app.nc     , PFTmap.nc      )
```

<div class="sub-section"></div>

**opa9.card**

Modify it by using:
- `CI_temp_salin.py` script (see [Initial condition files](#initial-condition-files))
- **Netcdf Editor App** (see [Boundary condition files](#boundary-condition-files))


```
vim COMP/opa9.card
```

```
mesh_mask= n

ListNonDel= (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/coordinate_files/[coordinates_file.nc]                    , coordinates.nc                          ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/[GRID_TYPE]/opa9/new_deepmip_corr_runoffs_ORCA2_depths.nc , runoffs_ORCA2_depths.nc                 ), \
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/OUT_CI_temp_salin/[file_data_1m_potential_temperature_nomask.nc]      , data_1m_potential_temperature_nomask.nc ), \
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/OUT_CI_temp_salin/[file_data_1m_salinity_nomask.nc]                   , data_1m_salinity_nomask.nc              ), \
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/OUT_CI_temp_salin/[file_sss_data.nc]                                  , sss_data.nc                             ), \
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/OUT_CI_temp_salin/[file_sst_data.nc]                                  , sst_data.nc                             ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/[GRID_TYPE]/opa9/[chlorophyll_surface.nc]                 , chlorophyll_surface.nc                  ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/[GRID_TYPE]/opa9/[K1rowdrg.nc]                            , K1rowdrg.nc                             ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/[GRID_TYPE]/opa9/[mask_itf.nc]                            , mask_itf.nc                             ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/[GRID_TYPE]/opa9/[sali_ref_clim_monthly.nc]               , sali_ref_clim_monthly.nc                ), \
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/[OUT_APP]_bathy_netcdf_flask_app.nc                         , bathy_meter.nc                          ), \
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/[OUT_APP]_ahmcoef_netcdf_flask_app.nc                       , ahmcoef.nc                              ), \
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/[OUT_APP]_heatflow_netcdf_flask_app.nc                      , geothermal_heating.nc                   ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/[GRID_TYPE]/opa9/[M2rowdrg.nc]                            , M2rowdrg.nc                             ), \
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/[OUT_APP]_sub_basins_netcdf_flask_app.nc                    , subbasins.nc                            )
```

<div class="alert-warning">
  If you use the standard grid (<b>182x149</b>) <code>[GRID_TYPE] = std_grid</code>
  <br>
  If you use the extended grid (<b>182x174</b>) <code>[GRID_TYPE] = ext_grid</code>
</div>

Files below depend on the period of the simulation and the grid type:
- `coordinates.nc`:

  In `/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/coordinate_files/` you will find `coordinates_paleorca2_yd.nc`
  which can handle simulations ranging from 0 up to 90Ma.

  If you want a more specific coordinates file you might find what you are looking for with a `grep -ir /.*coordinates.*\.nc *` command on other IRENE user spaces.

- `M2rowdrg.nc`:
  
  M2 files which are provided in `/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/[GRID_TYPE]/opa9/` are generic files with no tides.

  <div class="alert alert-info">More specific M2 files are available for standard grid (ask Jean-Baptiste Ladant or Yannick Donnadieu)</div>

<div class="sub-section"></div>

**pisces.card**

<div class="alert-warning">
  If you use the standard grid (<b>182x149</b>), <em>DO NOT</em> modify this file
  <br> 
  If you use the extended grid (<b>182x174</b>), take the template below
</div>

```
vim COMP/pisces.card
```

```
[BoundaryFiles]
List=   ()

ListNonDel= (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/ext_grid/pisces/data_DIC_nomask_large_grid_corr.nc      , data_DIC_nomask.nc      ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/ext_grid/pisces/data_Alkalini_nomask_large_grid_corr.nc , data_Alkalini_nomask.nc ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/ext_grid/pisces/data_O2_nomask_large_grid_corr.nc       , data_O2_nomask.nc       ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/ext_grid/pisces/data_NO3_nomask_large_grid_corr.nc      , data_NO3_nomask.nc      ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/ext_grid/pisces/data_PO4_nomask_large_grid_corr.nc      , data_PO4_nomask.nc      ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/ext_grid/pisces/data_Si_nomask_large_grid_corr.nc       , data_Si_nomask.nc       ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/ext_grid/pisces/data_DOC_nomask_large_grid_corr.nc      , data_DOC_nomask.nc      ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/ext_grid/pisces/data_Fer_nomask_large_grid_corr.nc      , data_Fer_nomask.nc      ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/ext_grid/pisces/dust.orca_large_grid_corr.nc            , dust.orca.nc            ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/ext_grid/pisces/bathy.orca_v1_large_grid_corr.nc        , bathy.orca.nc           ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/ext_grid/pisces/river.orca_large_grid_corr.nc           , river.orca.nc           ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/ext_grid/pisces/ndeposition.orca_large_grid_corr.nc     , ndeposition.orca.nc     ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/ext_grid/pisces/par.orca_large_grid_corr.nc             , par.orca.nc             ), \
            (/ccc/work/cont003/gen2212/gen2212/utils_CM5A2/default_templates/ext_grid/pisces/solubility.orca_large_grid_corr.nc      , solubility.orca.nc      )
```
Those files come from here:
```
/ccc/work/cont003/gen2212/p25ladan/BC_IPSLCM5A2/New_DeepMIP/BC_IPSL_PISCES_Large_grid/
```


<div class="sub-section"></div>

**stomate.card**

No modification

<div class="sub-section"></div>

**oasis.card**

Modify it:
```
vim COMP/oasis.card
```
```
[InitialStateFiles]
List=   (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/CPLRESTART/[CPL-SIM]/flxat_LMD9695_maskFrom_Unknown.nc, flxat.nc), \
        (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/CPLRESTART/[CPL-SIM]/sstoc_new_deepmip.nc, sstoc.nc)

[BoundaryFiles]
List=   ()
ListNonDel= (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/mosaix_weights/grids_ORCA2.3xLMD9695_v2.nc                                , grids.nc                               ),\
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/mosaix_weights/masks_ORCA2.3xLMD9695_v2.nc                                , masks.nc                               ),\
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/mosaix_weights/areas_ORCA2.3xLMD9695_v2.nc                                , areas.nc                               ),\
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/mosaix_weights/rmp_tORCA2.3_to_tLMD9695_OceTemp_1stOrder_v2.nc            , rmp_torc_to_tlmd_MOSAIC.nc             ),\
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/mosaix_weights/rmp_tLMD9695_to_tORCA2.3_HeatWaterFluxes_1stOrder_v2.nc    , rmp_tlmd_to_torc_MOSAIC.nc             ),\
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/mosaix_weights/rmp_tLMD9695_to_tORCA2.3_calving_full_v2.nc                , rmp_tlmd_to_torc_MOSAIC_calvin.nc      ),\
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/mosaix_weights/rmp_tLMD9695_to_tORCA2.3_runoff_Quantity_to_Surfacic_v2.nc , rmp_tlmd_to_torc_MOSAIC_rivflu.nc      ),\
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/mosaix_weights/rmp_tLMD9695_to_uORCA2.3_WindStress_2ndOrder_v2.nc         , rmp_tlmd_to_uorc_BILINEAR_Corrected.nc ),\
            (/ccc/work/cont003/gen2212/[IRENE_USER]/BC_IPSLCM5A2/[OUT_APP]/mosaix_weights/rmp_tLMD9695_to_vORCA2.3_WindStress_2ndOrder_v2.nc         , rmp_tlmd_to_vorc_BILINEAR_Corrected.nc )

...

[ParametersFiles]
List=   (${SUBMIT_DIR}/PARAM/namcouple_${RESOL_CPL}_MX, namcouple)
```

- `flxat.nc` is produced from `CreateRestartAtm4Oasis.bash` (see [Oasis coupler](#oasis-coupler))
- `sstoc.nc` is produced from `CreateRestartOce4Oasis.bash` (see [Initial condition files](#initial-condition-files)) using as input the `amipbc_sst_1x1.nc` file provided in the **lmdz.card** from [LMDZ](#lmdz) simulation
- `mosaix_weights` files are produced from the **Netcdf Editor App** (see [Boundary condition files](#boundary-condition-files))


<div class="sub-section"></div>

**field_def_nemo-opa.xml**

No modification

<div class="sub-section"></div>

**namelist_ORCA2_cfg_paleo**

<div class="alert-warning">
  If you use the standard grid (<b>182x149</b>), set <code>jpjdta</code> and <code>jpjglo</code> to <code>149</code>
  <br> 
  If you use the extended grid (<b>182x174</b>), set <code>jpjdta</code> and <code>jpjglo</code> to <code>174</code>
</div>

```
vim PARAM/namelist_ORCA2_cfg_paleo
```
```
!-----------------------------------------------------------------------                        
&namcfg        !   parameters of the configuration                                            
!-----------------------------------------------------------------------                      
jpjdta      =     149/174               !  2nd    "         "    ( >= jpj )                    
jpjglo      =     149/174               !  2nd    -                  -    --> j  =jpjdta
```

Change also the numbers of procs to allocate:
```
jpni        =    4      !  jpni   number of processors following i (set automatically if < 1)
jpnj        =   25      !  jpnj   number of processors following j (set automatically if < 1)
jpnij       =  100      !  jpnij  number of local domains (set automatically if < 1)
```

<div class="alert-info">
  You can also play with <code>rn_rdt</code> and <code>nn_fsbc</code> to change the ocean time step (see <a href="#change-ocean-time-step">Change ocean time step</a>)
</div>

<div class="sub-section"></div>

**config.def_preind**

Set this file as the ones from your ELC and LMDZOR simulations or copy it from.

```
cp ../[ELC-SIM]/PARAM/config.def_preind PARAM/
```

<div class="sub-section"></div>

**run.def**

<div class="alert-warning">
  For the weight computation,
  <br>
  if you use <b>MOSAIC</b>, set <code>cpl_old_calving</code> to <code>y</code>
  <br>
  if you use <b>MOSAIX</b>, set <code>cpl_old_calving</code> to <code>n</code>
  <br>
  See more informations on <b>MOSAIC</b>/<b>MOSAIX</b> at <a href="https://paleoclim-cnrs.github.io/netcdf_editor_app/multi">Netcdf Editor App documentation</a>
</div>

```
vim PARAM/run.def
```
```
## cpl_old_calving : keep y for MOSAIC (legacy) weights, use n for MOSAIX weights (compatible with dynamico)
cpl_old_calving = y/n
```

<div class="sub-section"></div>

**namcouple_ORCA2xLMD9695_MX**

<div class="alert-warning">
  If you use the standard grid (<b>182x149</b>), make sure dimensions are in <code>182 149 96 96</code> or <code>96 96 182 149</code> format
  <br> 
  If you use the extended grid (<b>182x174</b>), make sure dimensions are in <code>182 174 96 96</code> or <code>96 96 182 174</code> format
</div>

Example:
```
vim PARAM/namcouple_ORCA2xLMD9695_MX
```
```
O_SSTSST  SISUTESW 1 <freq_coupling>  2  sstoc.nc  <output_mode>
182 149 96 96 torc  tlmd  LAG=<lag_oce>
```
OR
```
O_SSTSST  SISUTESW 1 <freq_coupling>  2  sstoc.nc  <output_mode>
182 174 96 96 torc  tlmd  LAG=<lag_oce>
```

<div class="alert-info">
  To make sure you replace all the values in the document without missing one you can use the following command in <b>vim</b> to replace occurences of <code>149</code> by <code>174</code> for example:
  <br>
  <pre><code>:%s/149/174/g</code>
<code>:%s/149/174/gc # Ask for confirmation before modification of each occurence</code></pre>
  </div>

<div class="sub-section"></div>

**orchidee.def_Choi**

Add `ALWAYS_INIT` and modify `RIVER_DESC` variables:

```
vim PARAM/orchidee.def_Choi
```
```
# and reinitialize it with VEGET_YEAR parameter.
# Then it is possible to change LAND USE file.
# If LAND_USE
# default = y
  VEGET_REINIT = n

#---------------ADDED----------------
# Allow dead PFT to grow back                                                                                      
ALWAYS_INIT = y 
#------------------------------------

...

# Create river description file
RIVER_DESC = n
```

<div class="sub-section"></div>

**Simulation**


Modify the job:
```
vim Job_[CPL-SIM]
```
```
PeriodNb=40
```

<div class="alert alert-info">
  Make sure <code>PeriodLength x PeriodNumber</code> runs in < 24h
  <li><code>PeriodLength</code> is defined in <code>config.card</code></li>
  <li><code>PeriodNumber</code> is defined in <code>Job_[CPL-SIM]</code></li>
</div>

Launch the simulation:
```
ccc_msub Job_[CPL-SIM]
```

# Prepare coupled simulation from restart

This imply you have a readily available coupled run to be used for your new simulation. 

Let's say we have for this a **CPL_REF** simulation starting at `0001-01-01` and ending at `0100-12-30`.

<div class="alert light-info">
  Location:
  <pre><code>$CCCWORKDIR/CM5A2.2/modipsl/config/IPSLCM5A2/</code></pre>
</div>

**config.card**
Copy the `config.card` from simulation **CPL_REF**:

```
cp CPL-REF/config.card .
```

Modify it:
```
#===========================
JobName=[NEW-CPL-SIM]
...
DateBegin=0101-01-01
DateEnd=200-12-30
#============================
...
#========================================================================
#D-- Restarts -
[Restarts]
...
OverRule=y
RestartDate=0100-12-30
RestartJobName=CPL-REF
RestartPath=/ccc/store/cont003/gen2212/[IRENE_USER]/IGCM_OUT/IPSLCM5A2/PROD/paleo
```

<div class="alert-info">
  <li>Set the <code>DateBegin</code> variable to make it start just after the end date of the <b>CPL-REF</b> simulation</li>
  <li>Set the <code>RestartDate</code> variable equal the end date on the <b>CPL-REF</b> simulation</li>
  <li>In this example length of <code>[NEW-CPL-SIM]</code> simulation will be 100 years, modify <code>DateEnd</code> if you want to change this</li>
</div>

Install job:
```
../../libIGCM/ins_job
```

<div class="alert light-info">
  Location:
  <pre><code>$CCCWORKDIR/CM5A2.2/modipsl/config/IPSLCM5A2/[NEW-CPL-SIM]/</code></pre>
</div>

Copy all the parametrization files from the previous **CPL-REF** simulation into the new one:
```
cp -r ../[CPL-REF]/COMP/* COMP/
cp -r ../[CPL-REF]/PARAM/* PARAM/
```

Launch the job:
```
ccc_msub Job_[NEW-CPL-SIM]
```

# General informations

## Relaunch a simulation

<div class="alert light-info">
  Location:
  <pre><code>$CCCWORKDIR/CM5A2.2/modipsl/config/IPSLCM5A2/[SIM_NAME]/</code></pre>
</div>

Check `run.card`, if status is still on `running`, change it to `fatal`.

Then use the command:

```
../../../libIGCM/clean_PeriodLength.job
```

and relaunch simulation:

```
ccc_msub Job_[SIM_NAME]
```

## Delete a running simulation
```
ccc_mdel [id]
```
`[id]` found with command `ccc_mpp | grep [IRENE_USER]`

## Change atmosphere time step

For this, variables need to be changed in  
```
$CCCWORKDIR/CM5A2.2/modipsl/config/IPSLCM5A2/[CPL-SIM]/PARAM/gcm.def_96x95
```
- `day_step` = Number of time steps in 1 day (must be integer)
- `i_physiq` = after how many (dynamical) atmosphere time steps physic is computed (must be integer)

By default `day_step = 480`, but in paleo, it is sometimes set to `720` or `960`. `iphysiq` must be adjusted consequently: it must vary linearly with `day_step`.

Example:
- `day_step = 480` and `iphysiq=_AUTO_ : DEFAULT = 10`
- So if `day_step = 960` therefore `iphysiq=_AUTO_ : DEFAULT = 20`

Explanation:
- if `day_step = 480` => `86400 / 480 = 180` => an atm time step is 3 min
- `iphysiq=10` means physic is calculated every 30min
- If `day_step = 960` => `86400 / 960 = 90` => an atm time step is 1 min 30 sec
- In order to the physics to be calculated every 30min `iphysi` must be equal to 20

## Change ocean time step

2 variables need to be changed in 
```
$CCCWORKDIR/CM5A2.2/modipsl/config/IPSLCM5A2/[CPL-SIM]/PARAM/namelist_ORCA2_cfg_paleo
```
- `rn_rdt`  is ocean time step in seconds (must be integer)
- `nn_fsbc` is after how many ocean time steps ocean is coupled with atmosphere (must be integer)

`nn_fsbc * rn_rdt` has to be equal to `28800` secondes to keep atmosphere/ocean coupling every 8 hours.

For example:
- **1h** ocean time step means `rn_rdt = 3600` and therefore `nn_fsbc = 8`
- **30min** ocean time step means `rn_rdt = 1800` and therefore `nn_fsbc = 16`


# TROUBLESHOOTING

<div class="alert-warning">
  This is not a comprehensive list of possible issues which can be encountered and even less so for the possible solves. It is only based on what I have experienced until now and might enhance with time as I encounter new issues.
</div>

## If simulation outputs are in $CCCSCRATCHDIR but not in $CCCSTOREDIR

<div class="alert-info">
  In this section, <code>[COMPONENT] =</code> <code>OCE</code>, <code>ATM</code>, <code>MBG</code>... 
</div>

**PackFrequency**

Depending on `PackFrequency` value in `config.card` of your simulation, output generated in:
```
$CCCSCRATCHDIR/IGCM_OUT/IPSLCM5A2/PROD/paleo/[SIMU_NAME]/[COMPONENT]/OUTPUT/
``` 
are then packed every `X` months/years and new files are generated in:
```
$CCCSTOREDIR/IGCM_OUT/IPSLCM5A2/PROD/paleo/[SIMU_NAME]/[COMPONENT]/OUTPUT/
```
But sometimes it doesn't work and output folder in *$CCCSTOREDIR* is empty.

<div class="alert alert-success">
  <b>Solving</b>:<br>
  Run command <code>../../../libIGCM/pack_output.job</code>.<br>
  Check doc <a href="https://forge.ipsl.jussieu.fr/igcmg_doc/wiki/Doc/CheckDebug#Startorrestartpostprocessingjobs">here</a>
</div>

**SeasonnalFrequency**

If you defined `SeasonnalFrequency` in `config.card` of your simulation, output generated in:
```
$CCCSCRATCHDIR/IGCM_OUT/IPSLCM5A2/PROD/paleo/[SIMU_NAME]/[COMPONENT]/OUTPUT/
```  
should be used to produce seasonnal outputs here:
```
$CCCSTOREDIR/IGCM_OUT/IPSLCM5A2/PROD/paleo/[SIMU_NAME]/[COMPONENT]/ANALYSE/SE
```
But sometimes, output folder in *$CCCSTOREDIR* can be empty as well.

<div class="alert-warning">
  First, make sure you have enough output files in *$CCCSCRATCHDIR*.<br>Example:<br> 
  If <code>SeasonnalFrequency=100Y</code> in <code>config.card</code> you must have at least the first 100 output files (if <code>PeriodLength = 1Y</code> in <code>config.card</code>) produced in: <pre><code>$CCCSCRATCHDIR/IGCM_OUT/IPSLCM5A2/PROD/paleo/[SIMU_NAME]/[COMPONENT]/OUTPUT/</code></pre> otherwise the seasonnal computation can't be launched.
</div>

<div class="alert alert-success">
  <b>Solving</b>:<br>
  Run command <code>../../../libIGCM/SE_Checker.job</code>.<br>
  Check doc <a href="https://forge.ipsl.jussieu.fr/igcmg_doc/wiki/Doc/CheckDebug#Startorrestartpostprocessingjobs">here</a>
</div>

## Hgardfou

When getting error `... hgardfou ...` in file `Debug/atm_debug_file` it means atmosphere model is getting unstable.

<div class="alert alert-success">
  <b>Solving</b>:<br>
  Try to change <code>ByPass_hgardfou_teta</code> and <code>ByPass_hgardfou_mats</code> to <code>y</code> in <code>COMP/lmdz.card</code>. This parameter will last for the first period being processed and then will be changed to <code>n</code>.
  <br>
  If it doesn't work, lowering atmosphere time step can solve the issue: (see <a href="#change-atmosphere-time-step">Change atmosphere time step</a>)
</div>

## Velocity larger than 20 m/s

When getting error `stpctl: the zonal velocity is larger than 20 m/s` in file `Debug/CPL-NDMIP-tpw-1x-V2_00010101_00011230_ocean.output` it means ocean model is getting unstable. 

At first glance it can be some numerical unstability, what is important to look at is if error occurs after some periods are done or straight away before first period is done.

- If simulation processes several periods before throwing this error:
  <div class="alert alert-success">
    <b>Solving</b>:<br>
    It can be fixed by setting <code>ByPass_addnoise_sst = y</code> in <code>oasis.card</code> and relaunching the period.<br>
    If it still doesn't work, generally, lowering ocean time step can solve the issue (see <a href="#change-ocean-time-step">Change ocean time step</a>)
  </div>

- If problems occurs straight away after simulation starts to run and no period is done:
  <div class="alert alert-success">
    <b>Solving</b>:<br>
    This case might be more tricky to solve, you have to check that all the files are ok: bathymetry, interpolation weights, runoff, etc... (This is kind of a "figure it out yourself" answer but there is no general solving unfortunately)
  </div>

## Simulation runs but no period are done

It might happen if you try to run a simu with extended grid. 
As historically the standard grid is in `182x149`, few parametrization files have these numbers hard coded and nc files in this grid format can be used as stock as well.
So setting new simulation with `182x174` grid size can be the origin of this issue.

You can start by checking param files like `COMP/pisces.card` and check the nc files used are in right format.
Check also `PARAM/namelist` files, some might have the `149` hard coded, so change it to `174`.

A `grep -r 149 *` might help to track the discidents


# QUESTIONS

If starting a new simu **CPL-extented** from a ref simu **CPL-REF** (which uses extended grid `182x174`)
After installing job from `config.card`, **CPL-extended** will have param files (`PARAM/namelist_ORCA2_cfg_paleo` and `PARAM/namcouple_ORCA2xLMD9695_MX`) in standard grid config with `149` hard coded.
Do we still need to replace `149` by `174` in these param files before launching `Job_CPL-extented` ?
