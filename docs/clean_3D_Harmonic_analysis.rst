======================================
clean 3D Harmonic build and submission
======================================

clean build
===========

::

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
  4007770.sdb


| **Does it WORK? (24 Oct 2016)**
| **OUTPUT SHOULD BE 3D harmonics, outputted monthly for June and July 2012. Also various daily files.**

  ``ls -lrt  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/``

**No values in 25hr diagnostics**
