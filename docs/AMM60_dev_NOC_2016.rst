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


Recompiled code from two different code bases. Tried running AMM60 switching in
these new executables.

Workflow::

Submit / wait / fix namelist change / submit / wait ...

In the end I decided to revert back to the old code base for now since the new
codebase is too untested and there may be easier ways to get timeaveraged maps
of dissipation.

----

The following recipe includes the two source codes tried. One is commented out.

Setup some directory aliases::

  export TDIR=/work/n01/n01/jelt/TRY
  #  export CDIR=$TDIR/dev_NOC_2016_r7342/NEMOGCM/CONFIG
  export CDIR=$TDIR/dev_r6998_ORCHESTRA_r7581/NEMOGCM/CONFIG


load modules::

    module swap PrgEnv-cray PrgEnv-intel
    module load cray-netcdf-hdf5parallel
    module load cray-hdf5-parallel


Clean out the TRY directory::

  cd $TDIR
  rm -rf *

Check out code and rename::

  #svn co http://forge.ipsl.jussieu.fr/nemo/svn/branches/2016/dev_NOC_2016@7342
  #mv dev_NOC_2016 dev_NOC_2016_r7342

  svn co https://forge.ipsl.jussieu.fr/nemo/svn/branches/NERC/dev_r6998_ORCHESTRA
  mv dev_r6998_ORCHESTRA dev_r6998_ORCHESTRA_r7581

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

Edit namelist_ref. Remove parameters that break the simulation::

  vi namelist_ref
  ...
  !ln_useCT    = .true.  ! use of Conservative Temp. ==> surface CT converted in Pot. Temp. in sbcssm

Trim ``run_counter.txt::

  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520


Submit job::

  ./run_nemo
  4204509.sdb

**CRASHED (21 Jan 2017)**

dom_init : domain initialization
 ~~~~~~~~

 ===>>> : E R R O R
         ===========

 misspelled variable in namelist namrun in reference namelist iostat =   19

 ===>>> : E R R O R
         ===========

 misspelled variable in namelist namrun in configuration namelist iostat =   19

**STOP**
*(24 Jan 2017)*




Further Errors when specifically trying dev_NOC_2016_r7342
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++


Error::

  less ocean.output

  ...
  Stefan-Boltzmann constant                 =   5.670000000000000E-008
  J/s/m^2/K^4

  conversion: degre ==> radian          rad =   1.745329251994330E-002

  smallest real computer value       rsmall =   1.110223024625157E-016

  ===>>> : E R R O R
  ===========

  misspelled variable in namelist nameos in reference namelist iostat =   19

  eos_init : equation of state
  ~~~~~~~~
  Namelist nameos : Chosen the Equation Of Seawater (EOS)
  TEOS-10 : rho=F(Conservative Temperature, Absolute  Salinity, depth)   ln
  _TEOS10 =  F
  EOS-80  : rho=F(Potential    Temperature, Practical Salinity, depth)   ln
  _EOS80  =  F
  S-EOS   : rho=F(Conservative Temperature, Absolute  Salinity, depth)   ln
  _SEOS   =  F

  ===>>> : E R R O R
  ===========

  Exactly one equation of state option must be selected

Try and edit the reference namelist to accomodate the change in EOS::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2
  vi namelist_ref

  !-----------------------------------------------------------------------
  &nameos        !   ocean physical parameters
  !-----------------------------------------------------------------------
     nn_eos      =  -1     !  type of equation of state and Brunt-Vaisala frequency
                                   !  =-1, TEOS-10
                                   !  = 0, EOS-80
                                   !  = 1, S-EOS   (simplified eos)
     ln_useCT    = .true.  ! use of Conservative Temp. ==> surface CT converted in Pot. Temp. in sbcssm

Switch for

::

  !-----------------------------------------------------------------------
  &nameos        !   ocean physical parameters
  !-----------------------------------------------------------------------
     ln_teos10   = .false.         !  = Use TEOS-10 equation of state
     ln_eos80    = .false.         !  = Use EOS80 equation of state
     ln_seos     = .false.         !  = Use simplified equation of state (S-EOS)
                                   !

Also edit namelist_cfg to switch to ln_teos10 = .true.::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2
  vi namelist_cfg
  ...
  !-----------------------------------------------------------------------
  &nameos        !   ocean physical parameters
  !-----------------------------------------------------------------------
     ln_teos10 = .true.    !  = Use TEOS-10 equation of state


Submit job::

   ./run_nemo
   4202926.sdb


Error::

 less ocean.output
 ...

  conversion: degre ==> radian          rad =   1.745329251994330E-002

  smallest real computer value       rsmall =   1.110223024625157E-016

  ===>>> : E R R O R
  ===========

  misspelled variable in namelist nameos in reference namelist iostat =   19

  eos_init : equation of state


Can't figure this one out. I can not find trace of ``rsmall`` in either old or new namelists


**STOP**


New Plan
++++++++

*(24 Jan 2017)*

Though it is desirable to get AMM60 up and running on a contemporary code base
perhaps the difficulties with the old case can be circumnavigated.

Maria pointed out that it is not really necessary to use the 25h averaging diagnostics
to make reasonable maps of dissipation. I could use the regular XIOS tools to output
multiples of 1 day for tke and dissipation.

----
