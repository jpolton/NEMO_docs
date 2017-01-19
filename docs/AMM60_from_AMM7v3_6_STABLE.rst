=======================================
AMM60 from AMM7 v3.6_STABLE environment
=======================================

PLAN:

* Start with Maria's 3d harmonics code base
* Migrate to AMM60 configuration via the namelists
* Try and keep the compiler flags as for AMM7 (don't want to pick up hacks)


PATH::

  /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r7564_harm3d/NEMOGCM/



----

Starting at `AMM7 3D harmonic analysis <AMM7_3D_Harmonic_analysis.html>`_

Build a clean version of the code base from r7564. This will match Maria's working AMM7 3D
harmonics code base. Verify the source code matches::

  cd /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r7564_harm3d/NEMOGCM/NEMO/OPA_SRC
  diff -crBN ../OPA_SRC /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/NEMO/OPA_SRC > tmp.txt
  grep "diff -cr" tmp.txt

Shows that only ``svn`` or non ``*90`` files differ.

Copy necessary bits from Maria's code. Define Maria and New directories::

  export MDIR=/work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  export NDIR=/work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r7564_harm3d/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP00
  export KDIR=/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_NSea

Copy run scripts::

  cp $MDIR/rsub $NDIR/.
  cp $MDIR/subm $NDIR/.

Copy xios executable (which AMM7 didn't seem to use..)::

  ln -s /work/n01/n01/jdha/ST/xios-1.0//bin/xios_server.exe $NDIR/.


Edit scripts (paths, config name, nit, name of exec, IJ)::

  vi rsub
  RUNDIR=/work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r7564_harm3d/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP00
  ...
  echo qsub -v  m=$m,y=$y,nit0=$nit0,ndate=$ndate  -o $RUNDIR/$ID-AMM60-$y-$month -N $ID$y$month $1
  qsub -v m=$m,y=$y,nit0=$nit0,ndate=$ndate -o $RUNDIR/$ID-AMM60-$y-$month -N $ID$y$month  $1

  vi subm
  #PBS -l select=92
  #PBS -l walltime=00:20:00
  #PBS -A n01-NOCL
  ##PBS -q short
  ...
  RUNDIR=/work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r7564_harm3d/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP00
  ...
  #test
  nit=576  # 2 days
  ...
  export EXEC=opa_harm3d
  ...
  IJ=192 # THIS NEEDS TO BE CHANGED FOR AMM60=2000 cores. AMM7=192 cores. But is not used in AMM60.
  #aprun -n$IJ ./$EXEC
  export NEMOproc=2000
  export XIOSproc=40
  aprun -b -n $NEMOproc -N 24 ./$EXEC : -N 5 -n $XIOSproc ./xios_server.exe >&stdouter

Copy executable::

  ln -s  $NDIR/../BLD/bin/nemo.exe $NDIR/opa_harm3d

Copy in coordinates file::

  cp $KDIR/WDIR/coordinates.bdy.nc $NDIR/.


  export GRIDDIR=/work/n01/n01/kariho40/NEMO/GRID         # Where to get forcings
  export RUNNAME=EXP_NSea
  export HOMEDIR=/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo              # Home Directory
  export JOBDIR=$HOMEDIR/$RUNNAME                 # Config directory




  #===============================================================
  # INPUT FILES
  #===============================================================
  #---------------------------------------------------------------
  # Coordinates
  #---------------------------------------------------------------
  echo `date`: Link coordinates
  ln -s $GRIDDIR/coordinates_AMM60.nc        $WDIR/coordinates.nc

  #---------------------------------------------------------------
  # Bathymetry
  #---------------------------------------------------------------
  echo `date`: Link Bathymetry
  ln -s $GRIDDIR/bathyfile_AMM60_nosmooth.nc $WDIR/bathy_meter.nc

  #---------------------------------------------------------------
  # XML files
  #---------------------------------------------------------------




Harmonising Namelists
=====================

For 2010 will use the ``namelist_cfg.template_skag_climate --> namelist_cfg`` (unless ``subm`` is changed).
Also need the ``namelist_top_cfg.template --> namelist_top_cfg`` (for passive tracers. Why?).
 Note that a tacer.stat is also supposed to get moved in the subm script after run
 completion but it isn't created. I suspect TOP is not used)


 Compare Karen and Maria REFERENCE namelists. For Maria's setup the reference namelist is ``namelist_cfg.template_skag_climate``
 and only get a few sed changes with keys like **__KEY__** in the subm script AND ``nitend_han = 25920``.::

   sdiff $MDIR/namelist_cfg.template_skag_climate $KDIR/namelist_ref
   sdiff $MDIR/../../SHARED/namelist_ref $KDIR/namelist_ref


Compare more Karen and Maria namelists::

  sdiff $MDIR/output_201201/namelist_cfg2 $KDIR/namelist_cfg
  sdiff $KDIR/LOGS/restart/namelist_ref $KDIR/LOGS/restart/namelist_cfg


**PLAN**. Options for modifying the namelists

#. Copy Karen's two namelists across and try and patch differences with Maria's
#. Create new namelist from ``namelist_cfg.template_skag_climate`` using ``$KDIR/namelist_cfg``.
Do this locally
#. Start from AMM15 namelists


Copy namelists from Karen's simulation::
  rm $NDIR/namelist_ref # symbolic link
  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/EXP_harmIT/namelist_* $NDIR/.

Just submit it and see what happens. (iodef.xml is default). There are no restart or forcing files::

  ./rsub subm 2010 1 1
  qsub -v m=1,y=2010,nit0=1,ndate=20100101 -o /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r7564_harm3d/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP00/GA-AMM60-2010-01 -N GA201001 subm
  4199527.sdb


Error::

  less output_201001/ocean.output
  ...
  File coordinates.nc* not found

  grep coordinates $NDIR/namelist_cfg
   cn_coords_file = 'coordinates.bdy.nc'

  cp $KDIR/WDIR/coordinates.bdy.nc $NDIR/.




ACTION: Fix and implement the above. Resubmit.

**Pending**

Took a detour. Does the old AMM60 harmonic attempts work with Maria's executable?




================


**TO DO**

* Sort IJ in subm
* Copy XML from maria
