======================================
AMM7  3D Harmonic build and submission
======================================

Plan
====

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

*12 Jan 17*

*Find restarts for 1982 simulation that Maria used.

*Copied in restarts from end July 2003

*Switched to different (harm3d) execuatable - this is probably more important than the restart file change:

#  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
#  ln -s opa_harm3d opa
*Removed this because EXEC=opa_harm3d is set in `subm`
Restored `ln -s /work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/BLD/bin/nemo.exe /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/opa`

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
---

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

Plan
====

Use Sarah's restarts. Run for 1 month with 3d harmonics. Then pick it up and output 3d harmonics for 3 months.
Copy restarts from
files_restart_198112 -> /work/n01/n01/slwa/NEMO/src/NEMO_V3.6_STABLE_r6232/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP06/files_restart_198112::

  cp files_restart_198112/* .

Submit::

  ./rsub subm 1982 1 1
  qsub -v m=1,y=1982,nit0=1,ndate=19820101 -o /work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7--1982-01 -N GA198201 subm
  4188366.sdb

**PENDING. Did the one month run work and produce sensible output for regular fields?** 

Then increase time to 3 months::

  vi subm
  nit=25920 # 90 days

And resubmit::

  ./rsub subm 1982 2 1


To-do
======

* Change to one month simulation so it completes::

  vi subm
  nit = ...

* Make sure the restarts are consistent with the tke scheme used (there are restarts linked to Sarah's space)
* Check velocity variables. Do they go wierd/quiet too? Is the forcing there? They are OK in Maria's output. They are not OK in my simulations.

* cold restart?


---



Try a different submission method? e.g.::

  ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1

No these look to be Sarah's code without the modifications for closure scheme or 3d harmonics

----

2. Compile and run using Maria's methods
========================================
