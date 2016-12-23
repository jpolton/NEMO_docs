======================================
clean 3D Harmonic build and submission
======================================

clean build
===========

::

  chmod a+rx /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/MY_SRC/*

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG

  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel

  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10 clean

  mkdir XIOS_AMM60_nemo_harmIT2/MY_SRC
  cp XIOS_AMM60_nemo_harmIT/MY_SRC/* XIOS_AMM60_nemo_harmIT2/MY_SRC/.
  cp XIOS_AMM60_nemo_harmIT/cpp_XIOS_AMM60_nemo_harmIT.fcm XIOS_AMM60_nemo_harmIT2/cpp_XIOS_AMM60_nemo_harmIT2.fcm

  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10

----


Make new EXPeriment
===================

Make new experiment directory::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2
  mkdir EXP_harmIT2

Copy files but not directories::

  cp ../XIOS_AMM60_nemo_harmIT/EXP_harmIT/* ../XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/.

Link restart files::

  mkdir EXP_harmIT2/RESTART
  ln -s  /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/EXPD376/RESTART/01264320  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/RESTART/.

Edit run_counter,  to run for 5 days)::

  cd EXP_harmIT2
  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520

Edit submission script, and maybe the wall time::

  vi submit_nemo.pbs
  #PBS -N AMM60_har2
  #PBS -l walltime=00:25:00

Edit run file for new directory path::

  vi run_nemo
  export RUNNAME=EXP_harmIT2
  export HOMEDIR=/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2

Check code to edit the harmonic analysis terms in the namelist file::

  less run_nemo
  ...
  273c\
  nit000_han = "$nn_it000"
  274c\
  nitend_han = "$nitend" " $JOBDIR/namelist_cfg > $WDIR/namelist_cfg

Check ``iodef.xml`` file for harmonic output and 25hr output::

  less iodef.xml


Submit job::

  ./run_nemo
  4011945.sdb


| **Does it WORK? (27 Oct 2016)**
| **OUTPUT SHOULD BE 3D harmonics, for 5 days. Also various daily files.**

cd  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/

| run_counter.txt is added to without garbled date
| lots of files in OUTPUT
| Issue with GDEPW not being complete (perhaps it is defined twice?) The map works north of mid Wales, though is wrapped westward. Perhaps W-grid items have a dimension length problem?
| I think the problem is with the 25h data

**ACTION: CHECK field_def.xml DEFINITIONS FOR THE 25H VARIABLES**
Get on with: ``/Users/jeff/python/ipynb/NEMO/3DHarmonicView.ipynb`` (loads data on /Volumes/archer)


----

Make new M2 EXPeriment, with only M2 output
===========================================

Make new experiment directory::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2
  mkdir EXP_harmIT2

Copy files but not directories::

  cp ../XIOS_AMM60_nemo_harmIT/EXP_harmIT/* ../XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/.

Link restart files::

  mkdir EXP_harmIT2/RESTART
  ln -s  /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/EXPD376/RESTART/01264320  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/RESTART/.

Edit run_counter,  to run for 5 days)::

  cd EXP_harmIT2
  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520

Edit submission script, and maybe the wall time::

  vi submit_nemo.pbs
  #PBS -N AMM60_har2
  #PBS -l walltime=00:25:00
  #PBS -A n01-NOCL

Edit run file for new directory path::

  vi run_nemo
  export RUNNAME=EXP_harmIT2
  export HOMEDIR=/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2

Check code to edit the harmonic analysis terms in the namelist file::

  less run_nemo
  ...
  273c\
  nit000_han = "$nn_it000"
  274c\
  nitend_han = "$nitend" " $JOBDIR/namelist_cfg > $WDIR/namelist_cfg

Check ``iodef.xml`` file for harmonic output and 25hr output. Only output M2 (or it is too large to play with)::

  less iodef.xml


Submit job::

  ./run_nemo
  4109840.sdb


| **Does it WORK? (12 Dec 2016)**
| **OUTPUT SHOULD BE 3D harmonics, for 5 days. Also various daily files.**
| **This is identical to previous run (ABOVE) but with only M2 tidal species AND with new PBS account**

cd  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/

22 Dec 16
Seems OK but I am missing the e[12][uvt] files for AMM60. Add them into iodef.xml. Trim run_counter.txt and resubmit::

  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520

  vi iodef.xml
  <file id="file51" name_suffix="_grid_T" description="ocean T grid variables" >
      <field field_ref="e1t"  />
      <field field_ref="e2t"  />
      <field field_ref="e3t"  />
  ...
  <file id="file53" name_suffix="_grid_U" description="ocean U grid variables" >
        <field field_ref="e1u"  />
        <field field_ref="e2u"  />
        <field field_ref="e3u"  />
  ...
  <file id="file54" name_suffix="_grid_V" description="ocean V grid variables" >
          <field field_ref="e1v"  />
          <field field_ref="e2v"  />
          <field field_ref="e3v"  />
  ...

  ./run_nemo
  4126747.sdb


| **Does GRID it WORK? (21 Dec 2016)**
| **OUTPUT SHOULD BE 3D harmonics, for 5 days. Also various daily files.**
| **I don't think that the 25h data is working**
| **This is identical to previous run (ABOVE) but with e1u, e2u, e1v, e2v, e1t, e2t**

cd  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/

The horizontal grids didn't work. It looks like they are not properly defined.
At best the output is a depth variable. **Action** check field_def.xml::

  vi /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/SHARED/field_def.xml

  <field id="e1v"          long_name="V-cell width"     standard_name="cell_width"      unit="m"       grid_ref="grid_V_3D" />
  <field id="e2v"          long_name="V-cell depth"     standard_name="cell_depth"      unit="m"       grid_ref="grid_V_3D" />         <field id="e3v"          long_name="V-cell thickness" standard_name="cell_thickness"  unit="m"       grid_ref="grid_V_3D" />
  <field id="e3v"          long_name="V-cell thickness"     standard_name="cell_thickness"   unit="m"  grid_ref="grid_V_3D" />

  <field id="e1u"          long_name="U-cell width"     standard_name="cell_width"      unit="m"       grid_ref="grid_U_3D" />
  <field id="e2u"          long_name="U-cell depth"     standard_name="cell_depth"      unit="m"       grid_ref="grid_U_3D" />
  <field id="e3u"          long_name="U-cell thickness"     standard_name="cell_thickness"   unit="m"  grid_ref="grid_U_3D" />

  <field id="e1t"          long_name="T-cell width"   standard_name="cell_width"   unit="m"   grid_ref="grid_T_3D"/>
  <field id="e2t"          long_name="T-cell depth"   standard_name="cell_depth"   unit="m"   grid_ref="grid_T_3D"/>
  <field id="e3t"          long_name="T-cell thickness"   standard_name="cell_thickness"   unit="m"   grid_ref="grid_T_3D"/>

  ./run_nemo
  4128089.sdb


| **Does GRID it WORK? (23 Dec 2016)**
| **OUTPUT SHOULD BE 3D harmonics, for 5 days. Also various daily files.**
| **I don't think that the 25h data is working**
| **This is identical to previous run (ABOVE) but with e1u, e2u, e1v, e2v, e1t, e2t now defined**

cd  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/
