=======================
AMM60 from dev_NOC_2016
=======================

*(19 Jan 17)*

PLAN:

* Compile recent v3.6 for AMM60. Future proofing.
* Test run in SBmooring code.
* Add in Maria's 3D harmonic code.
* When working move from TRY directory to something more permanent.

PATH::

  /work/n01/n01/jelt/NEMO/xx/NEMOGCM/

----

Setup some directory aliases::

  export TDIR=/work/n01/n01/jelt/TRY
  export CDIR=$TDIR/dev_NOC_2016_r7342/NEMOGCM/CONFIG


load modules::

    module swap PrgEnv-cray PrgEnv-intel
    module load cray-netcdf-hdf5parallel
    module load cray-hdf5-parallel


Clean out the TRY directory::

  cd $TDIR
  rm -rf *

Check out code and rename::

  svn co http://forge.ipsl.jussieu.fr/nemo/svn/branches/2016/dev_NOC_2016@7342
  mv dev_NOC_2016 dev_NOC_2016_r7342

Copy ARCH file::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/ARCH/arch-XC_ARCHER_INTEL.fcm $CDIR/../ARCH/.

Compile only with OPA_SRC::

  cd $CDIR
  ./makenemo -n XIOS_AMM60_nemo -m XC_ARCHER_INTEL -j 10

Fails. Remove spurious key_lim2, replace with the following (from AMM7)::

  vi XIOS_AMM60_nemo/cpp_XIOS_AMM60_nemo.fcm
  bld::tool::fppkeys key_dynspg_ts key_ldfslp key_zdfgls key_mpp_mpi \
                   key_netcdf4 \
                   key_nosignedzero key_traldf_c2d \
                   key_dynldf_c2d key_bdy key_tide key_vvl key_iomput \
                   key_diaharm

Maybe copy in Fred's bdy code into MY_SRC. (Came in an email from Maria). Files to be found at.
(I DO NOT DO THIS BUT NOTE IT HERE)::

 /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r7564_harm3d/NEMOGCM/CONFIG/XIOS_AMM60_nemo/MY_SRC/bdydyn3d.F90
 /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r7564_harm3d/NEMOGCM/CONFIG/XIOS_AMM60_nemo/MY_SRC/bdyini.F90

Later, copy in versions of Maria's diagnostics to implement the `3D Harmonic analysis <3D_Harmonic_analysis.html>`_

Build again::

 cd $CDIR
 ./makenemo clean
 ./makenemo -n XIOS_AMM60_nemo -m XC_ARCHER_INTEL -j 10

This produces a new executable which can be run from Karen's scripts::

  $CDIR/XIOS_AMM60_nemo/BLD/bin/nemo.exe

----

Move to Karen's linking and submission methodology.
Edit the link to the executable in the script ``run_nemo`` to point to this new executable::

  ls -l $CDIR/XIOS_AMM60_nemo/BLD/bin/nemo.exe
  cd  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/

  vi run_nemo
  ...
  rm  $JOBDIR/$EXEC
  #ln -s $CODEDIR/bin/$EXEC $JOBDIR/$EXEC
  ln -s **REPLACE_WITH_$CDIR_PATH**/XIOS_AMM60_nemo/BLD/bin/nemo.exe $JOBDIR/$EXEC

Trim ``run_counter.txt::

  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520


Submit job::

  ./run_nemo
  4202081.sdb

**PENDING**  

| **Does GRID it WORK? (19 Jan 2017)**
| **OUTPUT SHOULD BE 3D harmonics, for 5 days. Also various 25h files.**
