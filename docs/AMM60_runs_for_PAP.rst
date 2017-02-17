==================
AMM60 runs for PAP
==================

AIM:

- Simulate 3D area around the PAP site.
- 3D u,v,w,T,S hourly + Surface heat flux and surface stress
- Domain:: 16.0 - 16.5 W, 48.5 - 49.0 N
- Period:: Sep 1 2012 to Sep 1 2013

PATH: ``/work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_PAP/``

* Copy from EXP_NSea experiment using same executable
* More notes

----

Generate subdomain
++++++++++++++++++

NEMO_latlon_to_indices.ipynb

<!-- PAP 3D -->
<domain id="PAP3D" zoom_ibegin="083" zoom_jbegin="483" zoom_ni="024" zoom_nj="033" />

----

16 Feb 2017

Create a clean experiment from Karen’s code base with AVM in the XML output. (copied old EXP_NSea to EXP_NSea_old)::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago
  rsync -uartv  EXP_NSea_prelauch_copy/ EXP_PAP

  vi submit_nemo
  #PBS -N AMM60_PAP
  ##PBS -l select=84
  #PBS -l select=92
  #PBS -l walltime=00:20:00
  #PBS -A n01-NOCL
  #PBS -j oe
  #PBS -r n
  #PBS -m bea
  #PBS -M jelt@noc.ac.uk

  ..
  $JOBDIR/finish_nemo.sh
  $JOBDIR/run_nemo



  vi domain_def.xml
  <!-- PAP 3D -->
  <domain id="PAP3D" zoom_ibegin="083" zoom_jbegin="483" zoom_ni="024" zoom_nj="033" />


  vi run_nemo
  export RUNNAME=EXP_PAP
  ..
  export nrestart_max=3


  vi run_counter.txt
  1 1 7200 20100105
  2 7201 14400 7200=20100109
  3 1396801 1404000 1396800=20120831


  cp ../EXP_NSea/iodef.xml .
  vi iodef.xml
  ...
   <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->
    <file id="file51" name_suffix="_PAP3D_grid_T"   description="T grid variables">
     <field_group id="PAP3D_T" domain_ref="PAP3D">
      <field field_ref="e3t"  />
      <field field_ref="toce"         name="thetao"  long_name="sea_water_potential_temperature" />
      <field field_ref="soce"         name="so"      long_name="sea_water_salinity"              />
      <field field_ref="ssh"          name="zos"     long_name="sea_surface_height_above_geoid"      />
      <field field_ref="woce"         name="wo"      long_name="ocean vertical velocity" />
      <field field_ref="empmr"        name="wfo"      long_name="water_flux_into_sea_water"                     />
      <field field_ref="qsr"          name="rsntds"   long_name="surface_net_downward_shortwave_flux"           />
      <field field_ref="qt"           name="tohfls"   long_name="surface_net_downward_total_heat_flux"          />
    </field_group>
   </file>

   <file id="file3" name_suffix="_PAP3D_grid_U" description="ocean U grid variables" >
    <field_group id="PAP3D_U" domain_ref="PAP3D">
      <field field_ref="e3u"  />
      <field field_ref="uoce"         name="uo"      long_name="sea_water_x_velocity"  />
      <field field_ref="utau"         name="tauuo"   long_name="surface_downward_x_stress" />
    </field_group>
   </file>

   <file id="file4" name_suffix="_PAP3D_grid_V" description="ocean V grid variables" >
    <field_group id="PAP3D_V" domain_ref="PAP3D">
      <field field_ref="e3v"  />
      <field field_ref="voce"         name="vo"      long_name="sea_water_y_velocity" />
      <field field_ref="vtau"         name="tauvo"   long_name="surface_downward_y_stress" />
    </field_group>
   </file>

   <file id="file5" name_suffix="_PAP3D_grid_W" description="ocean W grid variables" >
    <field_group id="PAP3D_W" domain_ref="PAP3D">
      <field field_ref="e3w"  />
      <field field_ref="woce"         name="wo"      long_name="ocean vertical velocity" />
    </field_group>
   </file>

   </file_group>



Link in restart::

  ln -s  /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_NSea/RESTART/01396800 /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_PAP/RESTART/.


Submit::

  ./run_nemo
  4326612.sdb




Ran to completion but there was no output.
Copied the iodef.xml file from EXP_NSea
Trimmed off the run_counter.txt to 3 lines::

  vi run_counter.txt
  1 1 7200 20100105
  2 7201 14400 7200=20100109
  3 1396801 1404000 1396800=20120831


Resubmit::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_PAP
  ./run_nemo

**ACTION**: Share with Xiaolong. Q: Are the fluxes OK? Is the data OK?

----

Compression of output
=====================

Compress output data using the serial queue. Instead of trying to use netcdf compression, use gzip::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_PAP/
  cp ../EXP_NSea/gzip2012 .

  less gzip2012
  #!/bin/bash
  #PBS -N AMM60gz12
  #PBS -l select=serial=true:ncpus=1
  #PBS -l walltime=24:00:00
  #PBS -o AMM60gz12.log
  #PBS -e AMM60gz12.err
  #PBS -A n01-NOCL
  ###################################################
  #set up paths

  cd /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_PAP/OUTPUT
  gzip AMM60_1?_2012*nc

  # qsub -q serial <filename>
  ############################

Then submit the jobs::

  qsub -q serial gzip2012
  qsub -q serial gzip2013
