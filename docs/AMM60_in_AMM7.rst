Does AMM60 executable work in Maria's 3D harmonic run environment for AMM7
==========================================================================

Can I copy the AMM60 executable into the AMM7 working space in order to more
rapidly debug 3D harmonics on the short queue?
*probably not...*
I expect that the forcing files will immediately break as the
codes are compiled with different keys. Furthermore the restarts may not be compatible.

The following is mostly junk. Instead try moving just the *.xml files from AMM60 to AMM7 space.

----

*(30 Jan 2017)*

module add cray-hdf5-parallel
module load  cray-netcdf-hdf5parallel
module swap PrgEnv-cray PrgEnv-intel

Define some paths::

  export WDIR=/work/n01/n01/jelt
  export CDIR=$WDIR/NEMO/nemo_v3_6_STABLE_r7564_harm3d/NEMOGCM/CONFIG

Remove Fred's code and build again::

  rm /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r7564_harm3d/NEMOGCM/CONFIG/XIOS_AMM60_nemo/MY_SRC/*

  cd $CDIR
  ./makenemo clean
  ./makenemo -n XIOS_AMM7_nemo -m XC_ARCHER_INTEL -j 10


Copy fresh restart files from Dec 2011::

    cd $CDIR/XIOS_AMM7_nemo/EXP00
    for i in {0..9};     do cp 'files_restart_201112/restart_000'$i.nc 'restart_000'$i.nc; done
    for i in {10..99};   do cp 'files_restart_201112/restart_00'$i.nc  'restart_00'$i.nc; done
    for i in {100..191}; do cp 'files_restart_201112/restart_0'$i.nc   'restart_0'$i.nc; done


Edit the number of timestep in the simulation to do one month. Comment out the test cases::

  vi subm
  ...
  #nit=576  # 2 days
  #nit=4320  # 15 days
  #nit=25920 # 90 days

For 2012 the namelist comes from ``namelist_cfg.template_skag_climate``. The harmonic analysis is kept at 15 days because we don't care during spin up to June. **THIS WILL NEED TO BE CHANGED**::

  vi namelist_cfg.template_skag_climate
  ...
  nitend_han = 4320

Edit the ``iodef.xml``. Comment out the daily U,V,T,W,tides files. Otherwise a month will not complete on the short queue (Just M2 ran for 30days in 20mins). Lots of consituents commented out. Again this will need to be changed for the production run.


Make edits to use AMM60 executable.
Copy executable to EXPeriment directory::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/BLD/bin/nemo.exe $CDIR/XIOS_AMM7_nemo/EXP00/opa_AMM60harm3d

  vi subm
  ...
  export EXEC=opa_AMM60harm3d


Submit::

  ./rsub subm 2012 1 1
  qsub -v m=1,y=2012,nit0=1,ndate=20120101 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-2012-01 -N GA201201 subm
  4240252.sdb

**ACTION**

* Edit paths in rsub and subm. It looks like there are some lurling from_mane1 paths
* Check output in ``cd /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r7564_harm3d/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/OUTPUT``
* If migrating the executable does not work, could try moving just the *.xml files.
