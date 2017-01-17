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
