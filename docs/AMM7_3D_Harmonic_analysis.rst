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




Submit restart from Jan 1998 - for 3 months::

  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  ./rsub subm 1998 1 1

  qsub -v m=1,y=1998,nit0=1,ndate=19980101 -o /work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7--1998-01 -N GA199801 subm
  4185406.sdb

**PENDING (11 Jan 17)**

Check output. Mount archer over sshfs. Use ferret for quick and easy look at data::

  #cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  livmaf$ cd /Volumes/archer/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  ls -lrt GA_1d_19820201*nc
  ferret


----

2. Compile and run using Maria's methods
========================================
