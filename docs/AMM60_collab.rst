===============================
AMM60 outputs for collaboration
===============================

Summary
=======

1. Virtual Moorings along the shelf break - J.Hopkins
2. PAP site 3D output for PV analysis - Xiaolong.Yu
3. Glider simulations - Anastasiia.Domina
4. Baines Forcing - Matthew.Toberman
5. Tracer exchange - Chris.Wilson
6. FASTNEt cross shelf variances - Jason.Holt

----

1. Virtual Moorings along the shelf break - Jo.Hopkins
======================================================

2. PAP site 3D output for PV analysis - Xiaolong.Yu
===================================================

Notes: `AMM60_runs_for_PAP <AMM60_runs_for_PAP.html>`_

3D u,v,w,T,S hourly
Surface heat flux and surface stress

16.0 - 16.5 W, 48.5 - 49.0 N
Sep 1 2012 to Sep 1 2013

NEMO_latlon_to_indices.ipynb

<!-- PAP 3D -->
<domain id="PAP3D" zoom_ibegin="083" zoom_jbegin="483" zoom_ni="024" zoom_nj="033" />


3. Glider simulations - Anastasiia.Domina
=========================================

code:: NEMO_latlon_to_indices.ipynb

lat: ['48.307', '48.307', '48.997', '48.997']
lon: ['-9.903', '-9.003', '-9.903', '-9.003']

        <!-- Celtic Sea Shelf Break -->
        <domain id="CSeaSB" zoom_ibegin="343" zoom_jbegin="444" zoom_ni="038" zoom_nj="043" />

Note that this is a subset of:
  <domain id="BOX_D376" zoom_ibegin="0315" zoom_jbegin="0400" zoom_ni="85" zoom_nj="100" />

  Dates:  165-181 days of 2012 (13-29 June 2012 ~ 17 days) + week before
  June 2012.

4. Baines Forcing - Matthew.Toberman
====================================
Wants 3D z-coords for AMM60.

code:: NEMO_write_e3t_netcdf.ipynb

data:: /projectsa/pycnmix/jelt/AMM60/AMM60_e3t_mean.nc.gz

sftp AMM60 data from archer to ftp site::

  ssharcher
  cd /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_NSea/OUTPUT
  sftp jeltpub@livftp01.nerc-liv.ac.uk
  sftp> cd data
  sftp> mput AMM60_1d*gz

  qsub -q serial gzip2010
  qsub -q serial gzip2013
  qsub -q serial gzip2011_06_12
  qsub -q serial gzip2012_06_12


5. Tracer exchange - Chris.Wilson
=================================
hourly surface velocity


6. FASTNEt cross shelf variances
================================

Date:: 17 Feb 2017

Output 25h surface velocities to investigate particle spreading rates. Drogued at 50m.
Run for two months. Output data with the 3D harmonic.
Run for two months and also output 25hr velocities for dispersal analysis.
Try 6hour queue (expect 5:30hrs). 31 May 2012 (1264321) -- 27 July 2012 (1346400)

data:: cd /work/n01/n01/jelt//NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/OUTPUT

Chat with JohnH *(20 Feb 16* Monthly transport maps would be good. Drifter positions every 24 hours, from 25hr averaged velocities, with water depth would be good.
(Dates to be determined)
