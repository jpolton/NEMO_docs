*The thread that led to this text was severed when I found the trunk simulation had problems*

Second Run - for 2 month period.
================================

Make new EXPeriment::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT
  mkdir EXP_harmIT2

Copy files but not directories::

  cp EXP_harmIT/* EXP_harmIT2/.

Link restart files::

  mkdir ../EXP_harmIT2/RESTART
  ln -s  /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/EXPD376/RESTART/01264320  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/EXP_harmIT2/RESTART/.

Edit run_counter,  to run for two months (June and July 2012 = 87,840 mins)::

  cd EXP_harmIT2
  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1352160

Edit submission script, and maybe the wall time::

  vi submit_nemo.pbs
  #PBS -N AMM60_har2
  #PBS -l walltime=06:00:00

Edit run file for new directory path::

  vi run_nemo
  export RUNNAME=EXP_harmIT2

Inserted code to edit the harmonic analysis terms in the namelist file::

  vi run_nemo
  ...
  273c\
  nit000_han = "$nn_it000"
  274c\
  nitend_han = "$nitend" " $JOBDIR/namelist_cfg > $WDIR/namelist_cfg

Edit ``iodef.xml`` file to output at the end of the month (later will do "2m" frequency)::

      <file_group id="1m" output_freq="1mo" output_level="10" enabled=".TRUE."/>   <!-- real monthly files -->
        <file id="file8" name_suffix="_Tides" description="tidal harmonics" >
          <field field_ref="e3t"  />
          <field field_ref="gdept"/>
          <field field_ref="M2x_ro"       name="M2x_ro"   long_name="M2 ro   real part"                       />
          <field field_ref="M2y_ro"       name="M2y_ro"   long_name="M2 ro  imaaginary part"                  />
          ...

Also add in 25 hour and daily diagnostics::

  <file_group id="1d" output_freq="1d"  output_level="10" enabled=".TRUE." > <!-- 1d files -->
    <file id="file51" name_suffix="_grid_T" description="ocean T grid variables" >
      <field field_ref="e3t"  />
      <field field_ref="gdept"/>
      <field field_ref="temper25h"    name="temper25h" long_name="sea_water_potential_temperature"    />
      <field field_ref="salin25h"     name="salin25h" long_name="sea_water_salinity"                  />
      <field field_ref="ssh25h"       name="ssh25h"   long_name="sea_surface_height_above_geoid"      />
      <field field_ref="mldr10_1"     name="mldr10_1" long_name="Mixed Layer Depth 0.01 ref.10m"      />
    </file>

    <file id="file53" name_suffix="_grid_U" description="ocean U grid variables" >
      <field field_ref="e3u"  />
      <field field_ref="gdepu"  />
      <field field_ref="ssu"          name="uos"     long_name="sea_surface_x_velocity"    />
      <field field_ref="uoce"         name="uo"      long_name="sea_water_x_velocity" />
      <field field_ref="vozocrtx25h"  name="uo25h"   long_name="sea_water_x_velocity" />
      <field field_ref="ubar"         name="ubar"    long_name="barotropic_x_velocity" />
    </file>

    <file id="file54" name_suffix="_grid_V" description="ocean V grid variables" >
      <field field_ref="e3v"  />
      <field field_ref="gdepv"  />
      <field field_ref="ssv"          name="vos"     long_name="sea_surface_y_velocity"    />
      <field field_ref="voce"         name="vo"      long_name="sea_water_y_velocity" />
      <field field_ref="vomecrty25h"  name="vo25h"   long_name="sea_water_y_velocity" />
      <field field_ref="vbar"         name="vbar"    long_name="barotropic_y_velocity" />
    </file>

    <file id="file55" name_suffix="_grid_W" description="ocean W grid variables" >
      <field field_ref="e3w"  />
      <field field_ref="gdepw"  />
      <field field_ref="vomecrtz25h"  name="wo25h"   long_name="ocean vertical velocity" />
      <field field_ref="eps25h"       name="eps25h"  long_name="TKE dissipation rate 25h " />
      <field field_ref="N2_25h"       name="N2_25h"  long_name="25h mean Brent-Viasala " />
      <field field_ref="S2_25h"       name="S2_25h"  long_name="25h squared shear" />
      <field field_ref="tke25h"       name="TKE25h"  long_name="25h vertical kinetic energy" />
    </file>
  </file_group>

Submit job::

  ./run_nemo
  3985055.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  3985055.sdb     jelt     standard AMM60_har2    --   92 220    --  06:00 Q   --


| **Does it WORK? (11 Oct 2016)**
| **OUTPUT SHOULD BE 3D harmonics, outputted monthly for June and July 2012. Also various daily files.**

  ``ls -lrt  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/EXP_harmIT2/``

----

**12 Oct 2016**

| ``run_count.txt`` was edited for next run
| core dump in WDIR. Died in 19s
| Looks like the ``iodef.xml`` file is broken:

::

  EXP_harmIT2/LOGS/restart> less stdouterr

  > Error [CXMLParser::ParseStream(StdIStream & stream)] : In file '/work/n01/n01/jdha/ST/xios-1.0/src/xml_parser.cpp', line 104 ->
  Error is occuring when parsing XML flux from <./iodef.xml> at character 34429 line 380 column 2

  </simulation>
  x^
   Error : expected element name
  > Error [CXMLParser::ParseStream(StdIStream & stream)] : In file '/work/n01/n01/jdha/ST/xios-1.0/src/xml_parser.cpp', line 104 ->

What is the differece between ``EXP_harmIT/iodef.xml`` and ``EXP_harmIT2/iodef.xml``?::

  EXP_harmIT> diff iodef.xml ../EXP_harmIT2/iodef.xml
  26c26,66
  <       <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1d files  EDITTED TO MAKE 1H files -->
  ---
  >
  >       <file_group id="1d" output_freq="1d"  output_level="10" enabled=".TRUE." > <!-- 1d files -->
  >         <file id="file51" name_suffix="_grid_T" description="ocean T grid variables" >
  >           <field field_ref="e3t"  />
  >           <field field_ref="gdept"/>
  >           <field field_ref="temper25h"    name="temper25h" long_name="sea_water_potential_temperature"    />
  >           <field field_ref="salin25h"     name="salin25h" long_name="sea_water_salinity"                  />
  >           <field field_ref="ssh25h"       name="ssh25h"   long_name="sea_surface_height_above_geoid"      />
  >           <field field_ref="mldr10_1"     name="mldr10_1" long_name="Mixed Layer Depth 0.01 ref.10m"      />
  >         </file>
  >
  >         <file id="file53" name_suffix="_grid_U" description="ocean U grid variables" >
  >           <field field_ref="e3u"  />
  >           <field field_ref="gdepu"  />
  >           <field field_ref="ssu"          name="uos"     long_name="sea_surface_x_velocity"    />
  >           <field field_ref="uoce"         name="uo"      long_name="sea_water_x_velocity" />
  >           <field field_ref="vozocrtx25h"  name="uo25h"   long_name="sea_water_x_velocity" />
  >           <field field_ref="ubar"         name="ubar"    long_name="barotropic_x_velocity" />
  >         </file>
  >
  >         <file id="file54" name_suffix="_grid_V" description="ocean V grid variables" >
  >           <field field_ref="e3v"  />
  >           <field field_ref="gdepv"  />
  >           <field field_ref="ssv"          name="vos"     long_name="sea_surface_y_velocity"    />
  >           <field field_ref="voce"         name="vo"      long_name="sea_water_y_velocity" />
  >           <field field_ref="vomecrty25h"  name="vo25h"   long_name="sea_water_y_velocity" />
  >           <field field_ref="vbar"         name="vbar"    long_name="barotropic_y_velocity" />
  >         </file>
  >
  >         <file id="file55" name_suffix="_grid_W" description="ocean W grid variables" >
  >           <field field_ref="e3w"  />
  >           <field field_ref="gdepw"  />
  >           <field field_ref="vomecrtz25h"  name="wo25h"   long_name="ocean vertical velocity" />
  >           <field field_ref="eps25h"       name="eps25h"  long_name="TKE dissipation rate 25h " />
  >           <field field_ref="N2_25h"       name="N2_25h"  long_name="25h mean Brent-Viasala " />
  >           <field field_ref="S2_25h"       name="S2_25h"  long_name="25h squared shear" />
  >           <field field_ref="tke25h"       name="TKE25h"  long_name="25h vertical kinetic energy" />
  >         </file>
  >       </file_group>
  >
  >       <file_group id="1m" output_freq="1mo" output_level="10" enabled=".TRUE."/>   <!-- real monthly files -->
  271c311
  <
  ---
  >
  280d319
  <       <file_group id="1m" output_freq="1mo" output_level="10" enabled=".TRUE."/> <!-- real monthly files -->


It looks like there may be an extra ``</file_group>`` in ``EXP_harmIT2/iodef.xml``. No this is OK
**ACTION** CHECK ``EXP_harmIT2/iodef.xml``. Not sure what XML output freq is needed.
**ACTION**. CHECK ``field_def.xml``: do all the additional 1 day variables exist?

Unhelpfully decided to make a new run. Comment out the 1 day output in ``iodef.xml``
Resubmit ONE month (30 day) simulation (left it on the 6 hour queue though only needs about 3 hours)::

  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1221120

  ./run_nemo
  3987463.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  3987463.sdb     jelt     standard AMM60_har2    --   92 220    --  06:00 Q   --

| **Does it WORK? (12 Oct 2016)**
| **OUTPUT SHOULD BE 3D harmonics, outputted monthly for June 2012.**

``cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/EXP_harmIT2/``

**(19 Oct 16)**
CORE dump. Though ``run_counter.txt`` got a new line


**Action this simulation is a bit redundant since the primary simulation does not create harmonic output that is readable**



*Old*: What might be going wrong with these harmonic outputs?
=============================================================

SYMPTOMS:

* rerun of SBmoorings experiment with 3D harmonic executable worked fine and produced virtual moorings (straight swap in executables)
* When harmonics are introduced in the XML file the simulations do not output and just run until wall-time, then dump stderrlog file
* Compiling with key_diaharm still produces the same 'running to wall-time and no output' problem.
* Missed field_def.xml changes

PLAN:

* Are the relevant harmonic compiler keys used? NO MISSING  key_diaharm. Edited the above
* NEED TO CLEAN NOTES AFTER AMM60_harm, and AMM60_har2 finish
* Could increase the harmonic XML output freq to hourly / could output harmonic at SBmooring.
* Do a straight swap from Karen’s to Maria's cp diaharm.F90_mane1 diaharm.F90 —> cleaner
* Test XML mods in AMM7 short queue
