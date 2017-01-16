======================================
AMM7  3D Harmonic build and submission
======================================

0. Plan
=======

* Copy from Maria
* Compile and run using her methods
* Update code base with latest v3.6_STABLE
* Compile and run
* Remove initial straight copied version
* Update to AMM60 configuration.

1. Copy and run code from Maria
===============================

Create empty directory tree::

  ~/work/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00

Synchronise EXP00 dir::

  rsync -uartv --delete  /work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/ ~/work/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00

  NB /work/n01/n01/mane1/AMM7_w is the 3D harmonic experiment. The alias points to /work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00

Edit submission script paths::

  vi subm
  #RUNDIR=/work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  RUNDIR=/work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00

  vi rsub
  #RUNDIR=/work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  RUNDIR=/work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00

Manually fix the restart files to appear. **(Perhaps this should not be done in this script as it will get repeated with each iteration)**::

  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  for i in {0..9};     do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA199712/restart_000'$i.nc 'restart_000'$i.nc; done
  for i in {10..99};   do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA199712/restart_00'$i.nc  'restart_00'$i.nc; done
  for i in {100..191}; do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA199712/restart_0'$i.nc   'restart_0'$i.nc; done

Try restart from end July 2003::

  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  for i in {0..9};     do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA200307/restart_000'$i.nc 'restart_000'$i.nc; done
  for i in {10..99};   do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA200307/restart_00'$i.nc  'restart_00'$i.nc; done
  for i in {100..191}; do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA200307/restart_0'$i.nc   'restart_0'$i.nc; done


Submit restart from Jan 1998 - for 3 months::

  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  ./rsub subm 1998 1 1

  qsub -v m=1,y=1998,nit0=1,ndate=19980101 -o /work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7--1998-01 -N GA199801 subm
  4185406.sdb


Note that the output here is for three months but the start date is manually set in the namelist file: `output_199801/namelist_cfg2`

Check output. Mount archer over sshfs. Use ferret for quick and easy look at data::

  #cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  livmaf$ cd /Volumes/archer/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  ls -lrt GA_1d_1982*nc
  ferret
  yes? use GA_1d_19820201_19820501_grid_W.nc
  yes? shade/x=100/y=150  log(TKE25H)

Maria's integrations work and do not show evidence of decaying with time.

With restart from restart_CAA199712 the 25h output decays very fast

----

*12 Jan 17*

* Find restarts for 1982 simulation that Maria used.

* Copied in restarts from end July 2003

* Switched to different (harm3d) execuatable - this is probably more important than the restart file change::

  ``cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00``
  ``ln -s opa_harm3d opa``

* Reversed this because EXEC=opa_harm3d is set in `subm`. Restored ``ln -s /work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/BLD/bin/nemo.exe /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/opa``

Submit::

  ./rsub subm 2003 8 1
  qsub -v m=8,y=2003,nit0=1,ndate=20030801 -o /work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7--2003-08 -N GA200308 subm
  4186749.sdb

Something happened and it crashed pretty quick::

  less GA200308.e4186749
  /var/spool/PBS/mom_priv/jobs/4186749.sdb.SC: line 226: [: -lt: unary operator expected


Submit again with non-unit nit0, so that calendar parameters are read from restart::

  ./rsub subm 2003 8 2
  qsub -v m=8,y=2003,nit0=2,ndate=20030801 -o /work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7--2003-08 -N GA200308 subm
  4187198.sdb

Same error. Switch back to other (Dec 97) restarts::

  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  for i in {0..9};     do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA199712/restart_000'$i.nc 'restart_000'$i.nc; done
  for i in {10..99};   do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA199712/restart_00'$i.nc  'restart_00'$i.nc; done
  for i in {100..191}; do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA199712/restart_0'$i.nc   'restart_0'$i.nc; done

Resubmit::

  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  ./rsub subm 1998 1 1
  qsub -v m=1,y=1998,nit0=1,ndate=19980101 -o /work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7--1998-01 -N GA199801 subm
  4187211.sdb

Note that  ``restart_CAA199712`` has 100 files and ``restart_CAA200307`` has 192 files. There should be 192. Some of the 1997 files are missing. Synchronise again::

  rsync -uartv /work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/restart_CAA199712/ /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/restart_CAA199712


Resubmit 2003 run::

  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  for i in {0..9};     do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA200307/restart_000'$i.nc 'restart_000'$i.nc; done
  for i in {10..99};   do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA200307/restart_00'$i.nc  'restart_00'$i.nc; done
  for i in {100..191}; do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA200307/restart_0'$i.nc   'restart_0'$i.nc; done

  ./rsub subm 2003 8 1

Problem with `difvho`. Netcdf output fails to write properly. (No time dimension, L=0 in ferret)::

  less GA199801.e4187211
  > Error [CNc4DataOutput::writeFieldData_ (CField*  field)] : In file '/home/n01/n01/slwa/work/NEMO/src/NEMO_V3.6_STABLE/NEMO/xios-1.0/src/output/nc4_data_output.cpp', line 1236 -> On writing field data: difvho
  ...

Comment out this variable from output::

  vi iodef.xml
  <file id="file25" name_suffix="_grid_W" description="ocean W grid variables" >
    <field field_ref="e3w"  />
    <field field_ref="gdepw"  />
    <field field_ref="woce"         name="wo"      long_name="ocean vertical velocity" />
    <!--
    <field field_ref="avt"          name="difvho"  long_name="ocean_vertical_heat_diffusivity" />
    -->
  </file>

Resubmit (``chmod a+rx restart_????.nc``)::

    ./rsub subm 2003 8 1
    qsub -v m=8,y=2003,nit0=1,ndate=20030801 -o /work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7--2003-08 -N GA200308 subm
    4188183.sdb


Still not working. Same error as before::

  less GA200308.e4188179
  /var/spool/PBS/mom_priv/jobs/4188179.sdb.SC: line 226: [: -lt: unary operator expected


Switch comment on month looper::

  #while [ $mm -le 1 ]; do
  while [ $mm -le $im2 ]; do

Resubmit::

  ./rsub subm 2003 8 1
  qsub -v m=8,y=2003,nit0=1,ndate=20030801 -o /work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7--2003-08 -N GA200308 subm
  4188202.sdb

Wall time exceeded.
Plots look OK-ish. E.g. shade /l=1 /k=40 TKE25H. But the TKE and EPS decays following initial conditions the first entry.


Check Maria's output. What can I learn about the settings. Did it work as expected?::

  livljobs ~ $ sshfs jelt@login.archer.ac.uk:/work/n01/n01 /work/jelt/mount_points/archer -o default_permissions,uid=18476,gid=18020,umask=022

  livljobs ~ $ cd /login/jelt/work/mount_points/archer/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00

  livljobs EXP00 $ ls -lrt GA*nc
  -rwxr-xr-x 1 jelt pol  8260603382 Dec 21 10:52 GA_1d_19820201_19820501_grid_T.nc
  -rwxr-xr-x 1 jelt pol  8260603382 Dec 21 10:53 GA_1d_19820201_19820501_grid_U.nc
  -rwxr-xr-x 1 jelt pol  8260603382 Dec 21 10:53 GA_1d_19820201_19820501_grid_V.nc
  -rwxr-xr-x 1 jelt pol 14314965659 Dec 21 10:54 GA_1d_19820201_19820501_grid_W.nc
  -rwxr-xr-x 1 jelt pol  6179036889 Dec 21 10:54 GA_1d_19820201_19820501_Tides.nc
  -rwxr-xr-x 1 jelt pol   209468708 Dec 21 10:55 GA_1m_19820201_19820501_grid_T.nc
  -rwxr-xr-x 1 jelt pol   141294638 Dec 21 10:56 GA_1m_19820201_19820501_grid_U.nc
  -rwxr-xr-x 1 jelt pol   141294638 Dec 21 10:57 GA_1m_19820201_19820501_grid_V.nc
  -rwxr-xr-x 1 jelt pol   273603514 Dec 21 10:57 GA_1m_19820201_19820501_grid_W.nc

  livljobs EXP00 $ ferret
             *** NOTE: Unable to create journal file ferret.jnl
   	NOAA/PMEL TMAP
   	FERRET v6.95
   	Linux 2.6.32-573.7.1.el6.x86_64 64-bit - 10/27/15
   	12-Jan-17 20:47

  yes? use GA_1d_19820201_19820501_grid_U.nc
  yes? sh da
       currently SET data sets:
      1> ./GA_1d_19820201_19820501_grid_U.nc  (default)
   name     title                             I         J         K         L         M         N
   NAV_LAT  Latitude                         1:297     1:375     ...       ...       ...       ...
   NAV_LON  Longitude                        1:297     1:375     ...       ...       ...       ...
   E3U      U-cell thickness                 1:297     1:375     1:51      1:90      ...       ...
   TIME_CENTERED
            Time axis                        ...       ...       ...       1:90      ...       ...
   TIME_CENTERED_BOUNDS
                                             1:2       ...       ...       1:90      ...       ...
   GDEPU    U-cell depth                     1:297     1:375     1:51      1:90      ...       ...
   UOS      sea_surface_x_velocity           1:297     1:375     ...       1:90      ...       ...
   UO       sea_water_x_velocity             1:297     1:375     1:51      1:90      ...       ...
   UO25H    sea_water_x_velocity             1:297     1:375     1:51      1:90      ...       ...
   UBAR     barotropic_x_velocity            1:297     1:375     ...       1:90      ...       ...

  yes? shade /x=100/y=150 UO

Velocities (daily) appear tp exibit a spring neap cycle. At the very least they are not decaying away from the restart.

Looking in from_mane1 directory, only maria's output has time dimension stored properly

----

*13 Jan*


Plan: Use Sarah's restarts. Run for 1 month with 3d harmonics. Then pick it up and output 3d harmonics for 3 months.
Copy restarts from
files_restart_198112 -> /work/n01/n01/slwa/NEMO/src/NEMO_V3.6_STABLE_r6232/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP06/files_restart_198112::

  cp files_restart_198112/* .

*Edit (16Jan) not sure why I wrote this*: Ensure turn off the harmonic analysis in namelist. *I don't see any action following this statement*
Use climatology EXCEPT for ``if [ $yy -ge 1990 -a $yy -le 2009 ]; then``::

  vi namelist_cfg.template_skag_climate
  nitend_han = 8640 ! 25920 ! 105120 ! 210528  ! Last time step used for harmonic analysis

Note the RUNDIR filepath for the output log files is set in rsub. Somehow this was changed back to Maria's path, which is why the qsub logs didn't work
Fix and submit::

  ./rsub subm 1982 1 1
  qsub -v m=1,y=1982,nit0=1,ndate=19820101 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7--1982-01 -N GA198201 subm
  4188705.sdb

Velocity blow up very fast. Try a cold start::

  vi subm
  ...
  rstart=.false.
  #   rstart=.true.


  ./rsub subm 1982 1 1
  qsub -v m=1,y=1982,nit0=1,ndate=19820101 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7--1982-01 -N GA198201 subm
  4188716.sdb

This hit the wall time but worked (Mangaged 19 days). Decrease runtime to complete on short queue (Cut down iodef.xml output so 1 month should now work)::

  vi subm
  nit=576  # 2 days

Make sure harmonic analysis will complete::

  vi namelist_cfg.template_skag_climate
  nitend_han = 576

  ./rsub subm 1982 1 1
  qsub -v m=1,y=1982,nit0=1,ndate=19820101 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-1982-01 -N GA198201 subm
  4188775.sdb

Pick up restart. Problem with renaming restarts so fix subm and do it manually here::

  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  for i in {0..9};     do mv 'GA_00000576_restart_000'$i.nc 'restart_000'$i.nc; done
  for i in {10..99};     do mv 'GA_00000576_restart_00'$i.nc 'restart_00'$i.nc; done
  for i in {100..191};     do mv 'GA_00000576_restart_0'$i.nc 'restart_0'$i.nc; done

Warm start::

  vi subm
  ...
  #rstart=.false.
  rstart=.true.

Run time 15 days (Cut down iodef.xml output so 1 month should now work)::

  vi subm
  nit=4320 # 15 days

Harmonic analysis duration 15 days (Cut down iodef.xml output so 1 month should now work)::

  vi namelist_cfg.template_skag_climate
  nitend_han = 4320

Submit::

  ./rsub subm 1982 1 1
  qsub -v m=1,y=1982,nit0=1,ndate=19820101 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-1982-01 -N GA198201 subm
  4188818.sdb

Run completed fine but the data is no good. **UBAR, TKE25H, EPS25H, M2X_SSH are empty-ish**

**Can not successfully rerun Maria's executable to produce same 3D tide output.**

The machinery for running seems OK but perhaps the executable is not good.

2. Compile new executable
=========================

Copy code from Maria::

  cd /work/n01/n01/mane1/V3.6_ST/NEMOGCM
  rsync -uart ARCH/ /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/ARCH
  rsync -uart EXTERNAL/ /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/EXTERNAL
  rsync -uart fcm-make/ /work/n01/n01/jelt/from_mane1/V3.6_S/fcm-make
  rsync -uart NEMO/ /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/NEMO
  rsync -uart SETTE/ /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/SETTE
  rsync -uart TOOLS/ /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/TOOLS
  cp License_CeCILL.txt /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/.
  cp CONFIG/XIOS_AMM7_nemo/cpp_XIOS_AMM7_nemo.fcm /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/.

Add compiler key::

  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM
  vi CONFIG/XIOS_AMM7_nemo/cpp_XIOS_AMM7_nemo.fcm
  ...
  key_diaharm


XIOS executables differ between that which I and Maria had previously used. Either using James' or Andrew build::

 cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/ARCH
 diff  arch-XC_ARCHER_INTEL.fcm /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/ARCH/arch-XC_ARCHER_INTEL.fcm
 35c35
 < %XIOS_HOME           /work/n01/n01/acc/XIOS_r484
 ---
 > %XIOS_HOME           /work/n01/n01/jdha/ST/xios-1.0

James' is newer. Use his::

 cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/ARCH/arch-XC_ARCHER_INTEL.fcm /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/ARCH/.

Compile::

  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel

  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG

  ./makenemo -n XIOS_AMM7_nemo -m XC_ARCHER_INTEL -j 10 clean
  ./makenemo -n XIOS_AMM7_nemo -m XC_ARCHER_INTEL -j 10

Copy executable to new EXPeriment directory::

  cp XIOS_AMM7_nemo/BLD/bin/nemo.exe XIOS_AMM7_nemo/EXP01/opa_harm3d

Also copy into old run directory to try it out::

  cp XIOS_AMM7_nemo/BLD/bin/nemo.exe XIOS_AMM7_nemo/EXP01/opa_harm3d_jp

Edit subm to use new executable::

  vi subm
  ...
  export EXEC=opa_harm3d_jp

Submit as a restart to see what happens::

  ./rsub subm 1982 2 1

Output files are not readable with ferret. All but the edges are emtpy

Recompile without key_diaharm
Resubmit as a restart. Note that this may be problematic since the restart data is not great::

  ./rsub subm 1982 2 1
  qsub -v m=2,y=1982,nit0=1,ndate=19820201 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-1982-02 -N GA198202 subm
  4189208.sdb

Data didn't work. Try again with a cold start::

  vi subm
  ...
  rstart=.false.
  #   rstart=.true.

  ./rsub subm 1982 2 1
  qsub -v m=2,y=1982,nit0=1,ndate=19820201 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-1982-02 -N GA198202 subm
  4189465.sdb


Empty files. Perhaps there is nothing to initialise the domain with (not this it seems to produce an error). TRy again with restarts::

  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  for i in {0..9};     do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA199712/restart_000'$i.nc 'restart_000'$i.nc; done
  for i in {10..99};   do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA199712/restart_00'$i.nc  'restart_00'$i.nc; done
  for i in {100..191}; do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA199712/restart_0'$i.nc   'restart_0'$i.nc; done

Try again with a warm start::

    vi subm
    ...
    #rstart=.false.
       rstart=.true.

    ./rsub subm 1982 2 1
    qsub -v m=2,y=1982,nit0=1,ndate=19820201 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-1982-02 -N GA198202 subm
    4189527.sdb

Crashed quicky because of excessive currents. *Post script: The warm start shohld have been done with ``/rsub subm 1982 1 1`` not Feb*
Try ramping up tides. Hmm can not see how to change the timestep (formally rn_rdt, which exists in ``output.namelist.dyn``)

Recompile with key_diaharm and resubmit::

  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel
  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG
  ./makenemo -n XIOS_AMM7_nemo -m XC_ARCHER_INTEL -j 10 clean
  ./makenemo -n XIOS_AMM7_nemo -m XC_ARCHER_INTEL -j 10
  cp XIOS_AMM7_nemo/BLD/bin/nemo.exe /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/opa_harm3d_jp


  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  for i in {0..9};     do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA199712/restart_000'$i.nc 'restart_000'$i.nc; done
  for i in {10..99};   do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA199712/restart_00'$i.nc  'restart_00'$i.nc; done
  for i in {100..191}; do cp '/work/n01/n01/mane1/AMM7_w/restart_CAA199712/restart_0'$i.nc   'restart_0'$i.nc; done



  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  ./rsub subm 1982 1 1
  qsub -v m=1,y=1982,nit0=1,ndate=19820101 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-1982-01 -N GA198201 subm
  4189784.sdb

Again, crashed quicky because of excessive currents.

Try with different restart files (restart)::

  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  for i in {0..9};     do cp 'files_restart_198112/restart_000'$i.nc 'restart_000'$i.nc; done
  for i in {10..99};   do cp 'files_restart_198112/restart_00'$i.nc  'restart_00'$i.nc; done
  for i in {100..191}; do cp 'files_restart_198112/restart_0'$i.nc   'restart_0'$i.nc; done

  ./rsub subm 1982 1 1
  qsub -v m=1,y=1982,nit0=1,ndate=19820101 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-1982-01 -N GA198201 subm
  4189941.sdb

Again, crashed quicky because of excessive currents.


Try a cold start::

  vi subm
  ...
  rstart=.false.
  #rstart=.true.

  ./rsub subm 1982 1 1

Ran but no valid data. Perhaps no initial condition.


Idea copy restarts from Maria, after successful simulation and integrate ONWARDS::

  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00

  for i in {0..9};     do cp '/work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/restart_000'$i.nc 'restart_000'$i.nc; done
  for i in {10..99};   do cp '/work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/restart_00'$i.nc  'restart_00'$i.nc; done
  for i in {100..191}; do cp '/work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/restart_0'$i.nc   'restart_0'$i.nc; done

Warm restart (check)::

  vi subm
  ...
  #rstart=.false.
  rstart=.true.

Use Maria's executable::

  vi subm
  ...
  export EXEC=opa_harm3d

Submit a follow on run for GA_1d_19820201_19820501_Tides.nc::

  ./rsub subm 1982 5 1
  qsub -v m=5,y=1982,nit0=1,ndate=19820501 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-1982-05 -N GA198205 subm
  4190062.sdb

Again, crashed quicky because of excessive currents.

Hmm...

Restarts crash because of excessive currents. This could perhaps be a wrong variable being read in as a velocity (if NEMO will allow this).
Can not do cold starts because I don't have the infracstructure to run from an initial condition.

Likely causes of the difficulties::

  1) wrong implementation of submission script
  2) wrong restart files
  3) wrong compiler keys
  4) Other user-error

Ask Maria...
