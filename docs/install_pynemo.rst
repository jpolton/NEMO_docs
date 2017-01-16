
Install PyNEMO
==============


When ready merge back into `SEAsia<SEAsia.html>`_

*(16 Jan 2017)* From above definitions::

  export WDIR=/home/n01/n01/jelt/work/lighthousereef/

Install PyNEMO (**svn checkout https://ccpforge.cse.rl.ac.uk/svn/pynemo** now  **https**)::

  cd ~
  module load anaconda
  conda create --name pynemo_env python scipy numpy matplotlib basemap netcdf4
  source activate pynemo_env
  conda install -c https://conda.anaconda.org/srikanthnagella seawater
  conda install -c https://conda.anaconda.org/srikanthnagella thredds_crawler
  conda install -c https://conda.anaconda.org/srikanthnagella pyjnius
  export LD_LIBRARY_PATH=/opt/java/jdk1.7.0_45/jre/lib/amd64/server:$LD_LIBRARY_PATH
  svn checkout https://ccpforge.cse.rl.ac.uk/svn/pynemo
  cd pynemo/trunk/Python
  python setup.py build

The following then breaks::

  python setup.py install --prefix ~/.conda/envs/pynemo
  cd $WDIR/INPUTS

With the following error report::

  running install
  Checking .pth file support in /home/n01/n01/jelt/.conda/envs/pynemo/lib/python2.7/site-packages/
  /home/n01/n01/jelt/.conda/envs/pynemo_env/bin/python -E -c pass
  TEST FAILED: /home/n01/n01/jelt/.conda/envs/pynemo/lib/python2.7/site-packages/ does NOT support .pth files
  error: bad install directory or PYTHONPATH

  You are attempting to install a package to a directory that is not
  on PYTHONPATH and which Python does not read ".pth" files from.  The
  installation directory you specified (via --install-dir, --prefix, or
  the distutils default setting) was:

      /home/n01/n01/jelt/.conda/envs/pynemo/lib/python2.7/site-packages/

  and your PYTHONPATH environment variable currently contains:

      '/home/y07/y07/cse/xalt/0.6.0/site:/home/y07/y07/cse/xalt/0.6.0/libexec:/usr/local/packages/cse/bolt/0.6/modules'

  Here are some of your options for correcting the problem:

  * You can choose a different installation directory, i.e., one that is
    on PYTHONPATH or supports .pth files

  * You can add the installation directory to the PYTHONPATH environment
    variable.  (It must then also be on PYTHONPATH whenever you run
    Python and want to use the package(s) you are installing.)

  * You can set up the installation directory to support ".pth" files by
    using one of the approaches described here:

    https://setuptools.readthedocs.io/en/latest/easy_install.html#custom-installation-locations


  Please make the appropriate changes for your system and try again.
