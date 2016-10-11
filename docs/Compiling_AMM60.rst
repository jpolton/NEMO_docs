Compiling AMM60
===============


Aim:
 * To recompile same code as Karen used:  ``/work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP``
 * Use James' XIOS file.

Load modules::

  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel

::

  jelt@eslogin004:~> module list
  Currently Loaded Modulefiles:
    1) modules/3.2.10.2                      17) cray-hdf5-parallel/1.8.13
    2) eswrap/1.3.2-1.020200.1274.0          18) intel/15.0.2.164
    3) switch/1.0-1.0502.57058.1.58.ari      19) cray-libsci/13.2.0
    4) craype-network-aries                  20) udreg/2.3.2-1.0502.9889.2.20.ari
    5) craype/2.4.2                          21) ugni/6.0-1.0502.10245.9.9.ari
    6) pbs/12.2.401.141761                   22) pmi/5.0.7-1.0000.10678.155.25.ari
    7) craype-ivybridge                      23) dmapp/7.0.1-1.0502.10246.8.47.ari
    8) cray-mpich/7.2.6                      24) gni-headers/4.0-1.0502.10317.9.2.ari
    9) packages-archer                       25) xpmem/0.1-2.0502.57015.1.15.ari
   10) bolt/0.6                              26) dvs/2.5_0.9.0-1.0502.1958.2.55.ari
   11) nano/2.2.6                            27) alps/5.2.3-2.0502.9295.14.14.ari
   12) leave_time/1.0.0                      28) rca/1.0.0-2.0502.57212.2.56.ari
   13) quickstart/1.0                        29) atp/1.8.3
   14) ack/2.14                              30) PrgEnv-intel/5.2.56
   15) xalt/0.6.0                            31) cray-netcdf-hdf5parallel/4.3.3.1
   16) epcc-tools/6.0                        32) cray-hdf5-parallel/1.8.14

----

Copy/synchronise with Karen's version of code (note if this is a big copy job it can be run using  ``qsub ~/scripts/rsync.pbs``)::

  rsync -uartvL --delete /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/ARCH/ /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/ARCH
  rsync -uartvL --delete /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/NEMO/  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/NEMO
  rsync -uartvL --delete /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/EXTERNAL/  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/EXTERNAL
  rsync -uartvL --delete /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/SETTE/ /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/SETTE
  rsync -uartvL --delete /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/TOOLS/ /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/TOOLS
  rsync -uartvL --delete /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/fcm-make/   /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/fcm-make

Get James' ``ARCH`` file::

  cp /work/n01/n01/jdha/ST/trunk/NEMOGCM/ARCH/arch-XC_ARCHER_INTEL.fcm /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/ARCH/arch-XC_ARCHER_INTEL.fcm

Make the config directory::

  mkdir /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG
  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG

Copy in the makefile::

  cp /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/makenemo /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/.

Make the configuration directory (with default options which generates a ``cfg.txt`` file with options: ``XIOS_AMM60_nemo OPA_SRC LIM_SRC_2 NST_SRC``)::

    ./makenemo -n XIOS_AMM60_nemo -m XC_ARCHER_INTEL -j 10

Copy MY_SRC code from Karen::

  rsync -uartvL --delete /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/MY_SRC/ /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/MY_SRC

Copy in the compiler flags file::

  cp /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/cpp_AMM60smago.fcm /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/cpp_XIOS_AMM60_nemo.fcm

Compile with ``XC_ARCHER_INTEL``::

  ./makenemo -n XIOS_AMM60_nemo -m XC_ARCHER_INTEL -j 10

----

Make a copy of this before getting all the boundary, grid and forcing files etc::

  cp -r XIOS_AMM60_nemo/ XIOS_AMM60_nemo_freshcompile

The following is done automatically in the run_nemo script:

Copy executable:

  cp  XIOS_AMM60_nemo/BLD/bin/nemo.exe XIOS_AMM60_nemo/EXP00/.

Link XIOS executable to EXPeriment directory::

  ln -s  /work/n01/n01/jdha/ST/xios-1.0/bin/xios_server.exe /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP00/xios_server.exe

----

**21 Sept 2016**
Recompile with debugging tools. Add ``-g -traceback``

Load modules::

  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel

::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/ARCH
  vi arch-XC_ARCHER_INTEL.fcm

  %FCFLAGS             -integer-size 32 -real-size 64 -O3 -fp-model source -zero -fpp -warn all -g -traceback
  %FFLAGS              -integer-size 32 -real-size 64 -O3 -fp-model source -zero -fpp -warn all -g -traceback

Make the configuration directory (with default options which generates a cfg.txt file with options: ``XIOS_AMM60_nemo OPA_SRC LIM_SRC_2 NST_SRC``)::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG
  ./makenemo -n XIOS_AMM60_nemo_debug -m XC_ARCHER_INTEL -j 10

Copy MY_SRC code from Karen::

  rsync -uartvL --delete /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/MY_SRC/ /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_debug/MY_SRC

Copy in the compiler flags file::

  cp /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/cpp_AMM60smago.fcm /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_debug/cpp_XIOS_AMM60_nemo_debug.fcm

Compile with ``XC_ARCHER_INTEL``::

  ./makenemo -n XIOS_AMM60_nemo_debug -m XC_ARCHER_INTEL -j 10

**THIS WORKED.**

Remove ``-g -traceback`` flags from ``arch-XC_ARCHER_INTEL.fcm``::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/ARCH
  vi arch-XC_ARCHER_INTEL.fcm
  %FCFLAGS             -integer-size 32 -real-size 64 -O3 -fp-model source -zero -fpp -warn all
  %FFLAGS              -integer-size 32 -real-size 64 -O3 -fp-model source -zero -fpp -warn all
