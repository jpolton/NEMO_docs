=====================================
AMM7 3D Harmonic build and submission
=====================================

0. Plan
=======

* Copy directory from Maria. Run
* Compile and run using Maria's methods
* Reference Maria's codebase to a fixed revision of v3.6_STABLE
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

Fix a bug in the submission script. Comment out ``ndate`` redefinition on line 119::

  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  vi subm
  ...
  #ndate=$yy$month1$day

Manually fix the restart files to appear. **(Perhaps this should not be done in this script as it will get repeated with each iteration)**::

  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  for i in {0..9};     do cp 'files_restart_198112/restart_000'$i.nc 'restart_000'$i.nc; done
  for i in {10..99};   do cp 'files_restart_198112/restart_00'$i.nc  'restart_00'$i.nc; done
  for i in {100..191}; do cp 'files_restart_198112/restart_0'$i.nc   'restart_0'$i.nc; done


Change bathy file::

    cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
    ln -s bathy_meter_orig.nc bathy_meter.nc

Use Maria's executable::

    vi subm
    ...
    export EXEC=opa_harm3d

Comment out ```difvho``, ``avt``, variable from output (it was associated with an error when things weren't working and I haven't reinstated it)::

      vi iodef.xml
      <file id="file25" name_suffix="_grid_W" description="ocean W grid variables" >
        <field field_ref="e3w"  />
        <field field_ref="gdepw"  />
        <field field_ref="woce"         name="wo"      long_name="ocean vertical velocity" />
        <!--
        <field field_ref="avt"          name="difvho"  long_name="ocean_vertical_heat_diffusivity" />
        -->
      </file>


Submit::

    ./rsub subm 1982 1 1
    qsub -v m=1,y=1982,nit0=1,ndate=19820101 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-1982-01 -N GA198201 subm
    4196147.sdb

This runs. AND the output looks sensible for TKE25H, EPS25H and M2X_SSH.



2. Compile new executable
=========================

Copy source code from Maria::

  cd /work/n01/n01/mane1/V3.6_ST/NEMOGCM
  rsync -uart ARCH/ /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/ARCH
  rsync -uart EXTERNAL/ /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/EXTERNAL
  rsync -uart fcm-make/ /work/n01/n01/jelt/from_mane1/V3.6_S/fcm-make
  rsync -uart NEMO/ /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/NEMO
  rsync -uart SETTE/ /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/SETTE
  rsync -uart TOOLS/ /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/TOOLS
  cp License_CeCILL.txt /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/.
  cp CONFIG/XIOS_AMM7_nemo/cpp_XIOS_AMM7_nemo.fcm /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/.

Add compiler tide harmonic key (key_diaharm), remove key_gen_IC::

  cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM
  vi CONFIG/XIOS_AMM7_nemo/cpp_XIOS_AMM7_nemo.fcm

  bld::tool::fppkeys key_dynspg_ts key_ldfslp key_zdfgls key_mpp_mpi \
                   key_netcdf4 \
                   key_nosignedzero key_traldf_c2d \
                   key_dynldf_c2d key_bdy key_tide key_vvl key_iomput \
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

Copy executable to EXPeriment directory::

  cp XIOS_AMM7_nemo/BLD/bin/nemo.exe XIOS_AMM7_nemo/EXP00/opa_harm3d

Edit subm to use new executable::

  vi subm
  ...
  export EXEC=opa_harm3d

Copy fresh restart files::

    cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
    for i in {0..9};     do cp 'files_restart_198112/restart_000'$i.nc 'restart_000'$i.nc; done
    for i in {10..99};   do cp 'files_restart_198112/restart_00'$i.nc  'restart_00'$i.nc; done
    for i in {100..191}; do cp 'files_restart_198112/restart_0'$i.nc   'restart_0'$i.nc; done

Submit::

    ./rsub subm 1982 1 1
    qsub -v m=1,y=1982,nit0=1,ndate=19820101 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-1982-01 -N GA198201 subm
    4196249.sdb

This WORKS!!  AND the output looks sensible for TKE25H, EPS25H and M2X_SSH.

----

**PLAN**

Now to replicate Maria's code need to create a restart in the summer. Say end of May. Then do a 3 month tidal analysis, for June, July, Aug.
So need a 5 month simulation. Launch 5 successive restarts from Jan 2012. Save the last restart set.

Copy fresh restart files from Dec 2011::

    cd /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
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

Submit run::

  ./rsub subm 2012 1 1
  qsub -v m=1,y=2012,nit0=1,ndate=20120101 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-2012-01 -N GA201201 subm
  4197124.sdb

Jan completed. Restart for Feb (on standard queue after 8pm)::

  ./rsub subm 2012 2 1
  qsub -v m=2,y=2012,nit0=1,ndate=20120201 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-2012-02 -N GA201202 subm
  4197610.sdb

Feb completed in 15mins on standard queue. Switch back to 20 mins on short queue::

  ./rsub subm 2012 3 1
  qsub -v m=3,y=2012,nit0=1,ndate=20120301 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-2012-03 -N GA201203 subm
  4198601.sdb

  ./rsub subm 2012 4 1
  qsub -v m=4,y=2012,nit0=1,ndate=20120401 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-2012-04 -N GA201204 subm
  4198656.sdb

  ./rsub subm 2012 5 1
  qsub -v m=5,y=2012,nit0=1,ndate=20120501 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-2012-05 -N GA201205 subm
  4198704.sdb

Backup the restarts from end of May 2012::

  mkdir files_restart_201205
  cp restart_????.nc files_restart_201205/.

Configure simulation to run for 3 months with all the XIOS output::

  vi iodef.xml
  *restore outputs*

  vi namelist_cfg.template_skag_climate
  nitend_han = 25920 ! 105120 ! 210528  ! Last time step used for harmonic analysis

  vi subm
  #PBS -l walltime=03:00:00
  ##PBS -q short
  ...
  nit=25920 # 90 days

Submit 3 month run::

  ./rsub subm 2012 6 1
  qsub -v m=6,y=2012,nit0=1,ndate=20120601 -o /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA-AMM7-2012-06 -N GA201206 subm
  4198851.sdb

The 3 month run took walltime-01:22:51.
A quick look with Ferret. Tides appear OK.

**ACTION: check grid_W.nc output**

**THIS IS THE ACTUAL END OF WHERE I HAD A THREE MONTH 3D TIDAL RUN WITH AMM7**

----

3. Other things learnt while debugging
======================================

Could submit again with non-unit nit0, so that calendar parameters are read from restart e.g.::

  ./rsub subm 2003 8 2

Edit the namelist parameters to do short queue runs.
e.g. Use climatology namelist EXCEPT for ``if [ $yy -ge 1990 -a $yy -le 2009 ]; then``::


Run time 15 days (Cut down iodef.xml output so 1 month should now work)::

      vi subm
      nit=4320 # 15 days

Harmonic analysis duration 15 days (Cut down iodef.xml output so 1 month should now work)::

      vi namelist_cfg.template_skag_climate
      nitend_han = 4320



4. Identify code changes in Maria's source
==========================================

Create a patch file for changes in the OPA_SRC directory. In the following there are duplicated lines for two different revisions.
I wasn't sure if a later revision would be preferable as it might have fewer changes. I think it just has different changes...
In implementing the following either follow r6204 OR r7564, not both.

Setup some directory aliases::

  export TDIR=/work/n01/n01/jelt/TRY
  export SDIR=/work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/NEMO/OPA_SRC
  export WDIR=/work/n01/n01/jelt

Clean out the TRY directory::

  cd $TDIR
  rm -rf *

Checkout a v3.6_STABLE at revision 6204/7564. (This corresponded to the highest version number in Maria's code)::

  svn co http://forge.ipsl.jussieu.fr/nemo/svn/branches/2015/nemo_v3_6_STABLE@6204
  svn co http://forge.ipsl.jussieu.fr/nemo/svn/branches/2015/nemo_v3_6_STABLE@7564

Create a link to the clean files in modified source::

  ln -s $TDIR/nemo_v3_6_STABLE/NEMOGCM/NEMO/OPA_SRC/ $SDIR/../OPA_SRC_r6204
  ln -s $TDIR/nemo_v3_6_STABLE/NEMOGCM/NEMO/OPA_SRC/ $SDIR/../OPA_SRC_r7564

For some reason OPA_SRC is recursively defined inside the target dir (for r6204)::

  rm $SDIR/../OPA_SRC_r6204/OPA_SRC

Using the syntax: ``diff -crB OrigSrc ModSrc > ModSrc.patch`` create a patch file,
where -c context, -r recursive (multiple levels dir), -B is to ignore Blank Lines, -N new files.
(Following http://linux.byexamples.com/archives/163/how-to-create-patch-file-using-patch-and-diff/)

Create a patch only for \*90 files. This is neatly done by creating a list of files that are not \*90 files and excluding them from the diff (diff has no include option)
I didn't do this for the r6204 patch::

  cd $SDIR/..
  find  OPA_SRC -type f | grep --text -vP "90$" | sed 's/.*\///' | sort -u > file1
  find  OPA_SRC_r7564/ -type f | grep --text -vP "90$" | sed 's/.*\///' | sort -u >> file1
  sort -u file1 > excludelist
  rm file[12]

  diff -crBN OPA_SRC_r6204 OPA_SRC  > OPA_SRC_r6204_harm3d.patch
  diff -crBN OPA_SRC_r7564 OPA_SRC -X excludelist > OPA_SRC_r7564_harm3d.patch

Make a new 'test' directory to patch::

  rsync -ravt  /work/n01/n01/jelt/TRY/nemo_v3_6_STABLE/NEMOGCM/NEMO/OPA_SRC/ OPA_SRC_harm3d
  cd OPA_SRC_harm3d/

Do a dry run first::

  patch --dry-run -p1 -i ../OPA_SRC_r6204_harm3d.patch
  patch --dry-run -p1 -i ../OPA_SRC_r7564_harm3d.patch

This is a bit heavy handed and patches all the files, including svn logs and things that do not need patching.
However a ``diff -r OPA_SRC_harm3d/ $SDIR`` returns a set of empty lines.



5. Check for differences between AMM7 and AMM60
===============================================


6. Build harmonic code from clean checkout
==========================================

Load modules::

  module swap PrgEnv-cray PrgEnv-intel
  module load cray-netcdf-hdf5parallel
  module load cray-hdf5-parallel

Setup some directory aliases::

  export WDIR=/work/n01/n01/jelt
  export CDIR=$WDIR/NEMO/nemo_v3_6_STABLE_r7564_harm3d/NEMOGCM/CONFIG

Clean out the TRY directory::

  cd $WDIR

Checkout a v3.6_STABLE at revision 7564. (This is currently the latest v3.6_STABLE
revision available)::

  svn co http://forge.ipsl.jussieu.fr/nemo/svn/branches/2015/nemo_v3_6_STABLE@7564

Change checked out repo directory name.
Have to use suffix harm3d since the source code in ``OPA_SRC`` will be modified
from checked out version::

  mv nemo_v3_6_STABLE/ NEMO/nemo_v3_6_STABLE_r7564_harm3d

Copy ARCH file::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/ARCH/arch-XC_ARCHER_INTEL.fcm $CDIR/../ARCH/.

Compile only with OPA_SRC::

  cd $CDIR
  ./makenemo -n XIOS_AMM60_nemo -m XC_ARCHER_INTEL -j 10

Fails. Remove spurious key_lim2, replace with the following (for AMM7)::

  vi XIOS_AMM60_nemo/cpp_XIOS_AMM60_nemo.fcm
  bld::tool::fppkeys key_dynspg_ts key_ldfslp key_zdfgls key_mpp_mpi \
                   key_netcdf4 \
                   key_nosignedzero key_traldf_c2d \
                   key_dynldf_c2d key_bdy key_tide key_vvl key_iomput \
                   key_diaharm

----

*Does not work*

Now have a MY_SRC directory. Copy original source code and apply patch there (exclude /.svn folders)::

  rsync -arvt --exclude=".*" $WDIR/NEMO/nemo_v3_6_STABLE_r6204/NEMOGCM/NEMO/OPA_SRC/* XIOS_AMM60_nemo/MY_SRC/.

Obtain and apply patch::

  cd $CDIR/XIOS_AMM60_nemo
  cp /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/NEMO/OPA_SRC_r7564_harm3d.patch .

  cd MY_SRC
  patch --dry-run -p1 -i ../OPA_SRC_r7564_harm3d.patch

The BDY patches seemed OK but all the others had issues. Could apply patch directly at OPA_SRC directory.

*END: DOES NOT WORK*.

----

Apply patch directly at OPA_SRC::

  cp /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/NEMO/OPA_SRC_r7564_harm3d.patch $CDIR/../NEMO/.
  cd $CDIR/../NEMO/OPA_SRC
  patch --dry-run -p1 -i ../OPA_SRC_r7564_harm3d.patch

If it doesn't return errors::

  patch -p1 -i ../OPA_SRC_r7564_harm3d.patch

This works. But does not preserve the source code.

*(19 Jan 2017)*

Copy in Fred's bdy code into MY_SRC. (Came in an email from Maria). Files to be found at::

  /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r7564_harm3d/NEMOGCM/CONFIG/XIOS_AMM60_nemo/MY_SRC/bdydyn3d.F90
  /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r7564_harm3d/NEMOGCM/CONFIG/XIOS_AMM60_nemo/MY_SRC/bdyini.F90

Build again::

  cd $CDIR
  ./makenemo clean
  ./makenemo -n XIOS_AMM60_nemo -m XC_ARCHER_INTEL -j 10

This produces a new executables, which was the same as the one generated from Maria's codebase
Except for changes with bdydyn3d.F90 and bdyini.F90 and modifications she made to nemogcm.F90 before I copied it.

Attempting to compile throws up an error associated with the ``sponge_factor`` array. Fixing it would mean fiddling with some more bdy files. If I don't need the sponge then I can just comment out this line.
To get the code to build I commented out the line starting ``sponge_factor``. This is OK-ish if the ln_sponge is not true, which it was not in Maria's run. (But was in Karen's)..


PLAN:

* Copy new executable into ``/work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/`` and try it out (ideally once the summer restarts are generated)

----
