=======================
AMM60 from dev_NOC_2016
=======================

PLAN:


* plan

PATH::

  /work/n01/n01/jelt/NEMO/xx/NEMOGCM/



----

Setup some directory aliases::

  export TDIR=/work/n01/n01/jelt/TRY
  export CDIR=$TDIR/dev_NOC/2016/NEMOGCM/CONFIG


load modules::

    module swap PrgEnv-cray PrgEnv-intel
    module load cray-netcdf-hdf5parallel
    module load cray-hdf5-parallel


Clean out the TRY directory::

  cd $TDIR
  rm -rf *

  svn co http://forge.ipsl.jussieu.fr/nemo/svn/branches/2016/dev_NOC_2016@7342
