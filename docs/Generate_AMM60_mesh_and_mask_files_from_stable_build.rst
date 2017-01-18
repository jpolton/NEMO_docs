====================================================
Generate AMM60 mesh and mask files from stable build
====================================================

Notes pulled from Evernote.
https://www.evernote.com/shard/s523/nl/2147483647/f3d110b9-fcdc-4882-8670-8df042cc949c/
*(written 22 Sept 2016)*

* Check out v3.6_stable and compile Karen’s code in XIOS_AMM60_nemo
* Run EXP_meshgen from restart with nn_msh = 1

PATH::

  /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r6939/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_meshgen

CONCLUSION:

**Can create an AMM60 mesh_mask.nc file from the v3.6_STABLE code using nn_msh = 1 as a restart.**

Modules::

  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel

----

Check out v3.6_stable and compile Karen’s code
==============================================

Then obtain the code::

  cd /work/n01/n01/jelt/NEMO
  svn co http://forge.ipsl.jussieu.fr/nemo/svn/branches/2015/nemo_v3_6_STABLE

# Get revision number and relabel the code::

  less /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE/.svn/entries
  mv nemo_v3_6_STABLE nemo_v3_6_STABLE_r6939

# Get James' ARCH file::

  cp /work/n01/n01/jdha/ST/trunk/NEMOGCM/ARCH/arch-XC_ARCHER_INTEL.fcm /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r6939/NEMOGCM/ARCH/arch-XC_ARCHER_INTEL.fcm

# make the configuration directory (with default options which generates a cfg.txt file with options: ``XIOS_AMM60_nemo OPA_SRC LIM_SRC_2 NST_SRC``)::

  cd CONFIG
  ./makenemo -n XIOS_AMM60_nemo -m XC_ARCHER_INTEL -j 10
THIS COMPILED OFF THE BAT WITH THE VARIOUS KEYS THAT WERE SELF GENERATED!

# copy in the compiler flags file (Karen’s run). Note, not copying any files from MY_SRC::

  cp /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/cpp_AMM60smago.fcm /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r6939/NEMOGCM/CONFIG/XIOS_AMM60_nemo/cpp_XIOS_AMM60_nemo.fcm

# Won’t compile because the ``jdha_init`` key is not defined without extra code. However, don’t need James’ code, that handle boundary files if only generating mesh data from a cold start.
Remove ``key_jdha_init``::

  vi XIOS_AMM60_nemo/cpp_XIOS_AMM60_nemo.fcm

# Try and compile again with Karen’s keys::

  ./makenemo -n XIOS_AMM60_nemo -m XC_ARCHER_INTEL -j 10 clean
  ./makenemo -n XIOS_AMM60_nemo -m XC_ARCHER_INTEL -j 10

Successful build!

----

Run ``EXP_meshgen`` from restart with ``nn_msh = 1``
====================================================

::

  cd /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r6939/NEMOGCM/CONFIG/XIOS_AMM60_nemo/
  mkdir EXP_meshgen

Copy run script into job directory::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_NSea/run_nemo EXP_meshgen/run_nemo
  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_NSea/submit_nemo.pbs EXP_meshgen/submit_nemo.pbs


Edit run script (Note I only want one restart at this stage)::

  vi EXP_meshgen/run_nemo
  export RUNNAME=EXP_meshgen
  export YEARrun='2012'
  export HOMEDIR=/work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r6939/NEMOGCM/CONFIG/XIOS_AMM60_nemo
  export nrestart_max=2 #31 (For one submission this number must equal the number of lines in run_counter.txt)

Edit submission script::

  vi EXP_meshgen/submit_nemo.pbs
  #PBS -N AMM60meshgen

Copy iodef.xml into job directory::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_notradiff_long/iodef_fastnet.xml EXP_meshgen/iodef.xml
  vi EXP_meshgen/iodef.xml

Copy domain_def.xml into job directory::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings/domain_def.xml EXP_meshgen/domain_def.xml

Copy finish_nemo.sh into job directory::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings/finish_nemo.sh EXP_meshgen/finish_nemo.sh



Link restart files::

  mkdir EXP_meshgen/RESTART
  ln -s  /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/EXPD376/RESTART/01264320  EXP_meshgen/RESTART/.

Create ``run_counter.txt`` into job directory (I don’t know the dates.
NB Karen’s numbers are quite large but I don’t see the restart files).
Note that the last line 2nd number must be +1 of the restart directory name.
BEWARE of extra white spaces in these lines as the ‘cutting'  will not work properly with them::

  vi EXP_meshgen/run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520

Copy in namelists::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_NSea/namelist_ref EXP_meshgen/.
  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_NSea/namelist_cfg EXP_meshgen/.

Edit namelist to generate mesh files::

  vi EXP_meshgen/namelist_cfg
    nn_msh      =    1      !  create (=1) a mesh file or not (=0)

Submit run::

  cd /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r6939/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_meshgen
  ./run_nemo
  3953845.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  3953845.sdb     jelt     standard AMM60meshg    --   92 220    --  00:05 Q   — nn_msh =1. HOW ARE THE MESH FILES?


**Job crashed. However ``mesh_mask\*.nc`` files contain grid information and is OK**::

  module load cray-netcdf
  ncdump -h mesh_mask_0208.nc
  ...
  double e3t_0(t, z, y, x) ;
  double e3w_0(t, z, y, x) ;

What went wrong: ``grep “E R R” WDIR/ocean.output``::

  jelt@eslogin005:/work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r6939/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_meshgen/WDIR> less ocean.output

   ===>>> : E R R O R
           ===========

   E R R O R :   misspelled variable in namelist nambdy in reference namelist iost
   at =   19

   ===>>> : E R R O R
           ===========

   E R R O R :   misspelled variable in namelist nambdy in configuration namelist
   iostat =   19
            nambdy
   Number of open boundary sets :            1

   ------ Open boundary data set            1 ------
   Boundary definition read from file coordinates.bdy.nc

This crash looks solvable...

However can I rebuild the mesh files and not bother?::

  cd  /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r6939/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_meshgen/WDIR
  /work/n01/n01/jelt/gmaya/NEMO/TOOLS/jREBUILD_NEMO/rebuild_nemo -t 40 -c 100 mesh_mask 2000

Put this command into the serial queue with a 20 minute queue (5mins too short)::

  cd /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r6939/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_meshgen
  qsub nc_mesh_build.pbs

This worked!

* 1st attempt had a problem, but it had to write some list files and terminated quite quickly (2sec)

* 2nd attempt exceeded 5 min wall time.

* 3rd attempt ,with 20mins. Took 4mins. No errors.

Copy output data to SAN and sanity check in Ferret::

  [livmaf:/scratch/jelt/tmp] scp jelt@login.archer.ac.uk:/work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r6939/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_meshgen/WDIR/mesh_mask.nc .
  yes? use mesh_mask.nc
  yes? shade /k=10  E3T_0
  ...
  yes? sh da
  currently SET data sets:
  1> ./mesh_mask.nc  (default)
  name     title                             I         J         K         L         M         N
  NAV_LON                                   1:1120    1:1440    ...       ...       ...       ...
  NAV_LAT                                   1:1120    1:1440    ...       ...       ...       ...
  NAV_LEV                                   ...       ...       1:51      ...       ...       ...
  TIME_COUNTER
                                        ...       ...       ...       1:1       ...       ...
  TMASK                                     1:1120    1:1440    1:51      1:1       ...       ...
  UMASK                                     1:1120    1:1440    1:51      1:1       ...       ...
  VMASK                                     1:1120    1:1440    1:51      1:1       ...       ...
  FMASK                                     1:1120    1:1440    1:51      1:1       ...       ...
  TMASKUTIL
                                        1:1120    1:1440    ...       1:1       ...       ...
  UMASKUTIL
                                        1:1120    1:1440    ...       1:1       ...       ...
  VMASKUTIL
                                        1:1120    1:1440    ...       1:1       ...       ...
  FMASKUTIL
                                        1:1120    1:1440    ...       1:1       ...       ...
  GLAMT                                     1:1120    1:1440    ...       1:1       ...       ...
  GLAMU                                     1:1120    1:1440    ...       1:1       ...       ...
  GLAMV                                     1:1120    1:1440    ...       1:1       ...       ...
  GLAMF                                     1:1120    1:1440    ...       1:1       ...       ...
  GPHIT                                     1:1120    1:1440    ...       1:1       ...       ...
  GPHIU                                     1:1120    1:1440    ...       1:1       ...       ...
  GPHIV                                     1:1120    1:1440    ...       1:1       ...       ...
  GPHIF                                     1:1120    1:1440    ...       1:1       ...       ...
  E1T                                       1:1120    1:1440    ...       1:1       ...       ...
  E1U                                       1:1120    1:1440    ...       1:1       ...       ...
  E1V                                       1:1120    1:1440    ...       1:1       ...       ...
  E1F                                       1:1120    1:1440    ...       1:1       ...       ...
  E2T                                       1:1120    1:1440    ...       1:1       ...       ...
  E2U                                       1:1120    1:1440    ...       1:1       ...       ...
  E2V                                       1:1120    1:1440    ...       1:1       ...       ...
  E2F                                       1:1120    1:1440    ...       1:1       ...       ...
  FF                                        1:1120    1:1440    ...       1:1       ...       ...
  MBATHY                                    1:1120    1:1440    ...       1:1       ...       ...
  MISF                                      1:1120    1:1440    ...       1:1       ...       ...
  ISFDRAFT                                  1:1120    1:1440    ...       1:1       ...       ...
  HBATT                                     1:1120    1:1440    ...       1:1       ...       ...
  HBATU                                     1:1120    1:1440    ...       1:1       ...       ...
  HBATV                                     1:1120    1:1440    ...       1:1       ...       ...
  HBATF                                     1:1120    1:1440    ...       1:1       ...       ...
  GSIGT                                     ...       ...       1:51      1:1       ...       ...
  GSIGW                                     ...       ...       1:51      1:1       ...       ...
  GSI3W                                     ...       ...       1:51      1:1       ...       ...
  ESIGT                                     ...       ...       1:51      1:1       ...       ...
  ESIGW                                     ...       ...       1:51      1:1       ...       ...
  E3T_0                                     1:1120    1:1440    1:51      1:1       ...       ...
  E3U_0                                     1:1120    1:1440    1:51      1:1       ...       ...
  E3V_0                                     1:1120    1:1440    1:51      1:1       ...       ...
  E3W_0                                     1:1120    1:1440    1:51      1:1       ...       ...
  RX1                                       1:1120    1:1440    ...       1:1       ...       ...
  GDEPT_1D                                  ...       ...       1:51      1:1       ...       ...
  GDEPW_1D                                  ...       ...       1:51      1:1       ...       ...
  GDEPT_0                                   1:1120    1:1440    1:51      1:1       ...       ...
  GDEPW_0                                   1:1120    1:1440    1:51      1:1       ...       ...


**CONCLUSION:**

**Can create an AMM60 mesh_mask.nc files from the v3.6_STABLE code using nn_msh = 1 as a restart.**
