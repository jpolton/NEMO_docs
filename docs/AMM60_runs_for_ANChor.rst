=====================
AMM60 runs for ANChor
=====================

AIM:

-  Make 2010 simulations of North Sea domain outputting hourly velocity, SSH and vertical viscosity for off-line particle tracking.

PATH: ``/work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_NSea/``

* This is a condensed version of the development process (AMM60 runs for ANChor - dev).
* There were two major problems: 1) generating mesh and mask files. 2) including AVM in the output.
* The former was solved with a clean build from a recent revision of v3.6_STABLE
* The latter was solved with a clean experiment from Karen’s code base.

----

26 Sept 2016

Create a clean experiment from Karen’s code base with AVM in the XML output. (copied old EXP_NSea to EXP_NSea_old)::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago
  rsync -uartv  EXP_NSea_prelauch_copy/ EXP_NSea

  vi submit_nemo
  #PBS -N AMM60_NSea
  #PBS -l walltime=00:20:00
  ..
    $JOBDIR/finish_nemo.sh
    $JOBDIR/run_nemo

  vi run_nemo
  export RUNNAME=EXP_NSea
  ..
  export nrestart_max=31

  vi run_counter.txt
  1 1 7200 20100105
  2 7201 14400 7200=20100109
  3 14401 21600 14400=20100114

  cp ../EXP_NSea2/iodef.xml .
  vi iodef.xml
      <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->
        <file id="file51" name_suffix="_NorthSea"  description="T grid variables">
          <field_group id="NorthSea" domain_ref="NorthSea">
            <field field_ref="ssh"          name="zos"      long_name="sea_surface_height_above_geoid"      />
            <field field_ref="avm"          name="avm"    long_name="vertical viscosity coefficient" />
            <field field_ref="uoce"        name="uo"      long_name="sea_water_x_velocity" />
            <field field_ref="voce"        name="vo"      long_name="sea_water_y_velocity" />
            <field field_ref="woce"        name="wo"      long_name="ocean vertical velocity" />
          </field_group>
        </file>
       </file_group>

(29 Sept 2016)
Added in daily mean T and S::

      <file_group id="1d" output_freq="1d"  output_level="10" enabled=".TRUE."> <!-- 1d files -->
        <file id="file31" name_suffix="_grid_T" description="ocean T grid variables" >
         <field_group id="NorthSeaTS" domain_ref="NorthSea">
          <field field_ref="toce"    name="thetao"  long_name="sea_water_potential_temperature" />
          <field field_ref="soce"    name="so"      long_name="sea_water_salinity"              />
         </field_group>
        </file>
      </file_group>

      vi domain_def.xml
        <domain_group id="grid_T">
        ..
          <!-- North Sea -->
          <domain id="NorthSea" zoom_ibegin="0560" zoom_jbegin="0600" zoom_ni="560" zoom_nj="600" />
          ..

Submit::

  ./run_nemo

----

**Check output is OK**

Copy AMM60_1h_20100120_20100124_NorthSea.nc to SAN. Check it out with FERRET (use NX)::

  livljobs6% cd /scratch/jelt/tmp/
  livljobs6% scp jelt@login.archer.ac.uk:/work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_NSea/OUTPUT/AMM60_1h_20100120_20100124_NorthSea.nc .

IS OUTPUT OK?
All looks fine (also u,v,w not shown)

----

21 Oct 2016

Extend the run through Aug 2010::

  vi run_counter.txt
  ...
  30 208801 216000 208800=20100529
  31 216001 223200 216000=20100603
  32 223201 230400 223200=20100608
  33 230401 237600 230400=20100613
  34 237601 244800 237600=20100618
  35 244801 252000 244800=20100623
  36 252001 259200 252000=20100628
  37 259201 266400 259200=20100703
  38 266401 273600 266400=20100708
  39 273601 280800 273600=20100713
  40 280801 288000 280800=20100718
  41 288001 295200 288000=20100723
  42 295201 302400 295200=20100728

Add 6 more integrations on. Need only change ``nrestart_max``::

  vi run_nemo
  export nrestart_max=47

Submit::

  ./run_nemo
  4003109.sdb

NOTE: I HAVE NOT EDITTED THIS run_nemo FILES WHICH MAY INCORRECTLY sed SOME VARIABLES
CHECK
``/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/EXP_harmIT/run_nemo``
OR ``/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings/run_nemo``
OR ``/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings2/run_nemo``
