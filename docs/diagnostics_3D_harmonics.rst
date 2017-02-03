Diagnostics of 3D Harmonics
===========================

*(3 Feb 17)*

**ACTIONS:**

* move good data from */scratch/tmp* to */projectsa*
* figure out what supporting grid data is also needed.
* AMM60 3 month run?
* How many tidal harmonics do I need to use (NB I don't need to output them all)

**Latex notes:** ~/Documents/my/IT_AMM60_16/IT_AMM60.pdf


**iPython notebook:** ~/python/ipynb/NEMO/internaltideharmonics_NEMO.ipynb


For analysis of data on ARCHER, must run notebook on livljobs because sshfs only
works on this machine.

Once data is checked and OK bring it to the SAN::

  #e.g. for 3 months of AMM7 data (June-Aug 2012)
  rsync -uartv jelt@login.archer.ac.uk:/work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/GA_1d_20120601_20120829*nc /scratch/jelt/tmp/.
  scp jelt@login.archer.ac.uk:/work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/coordinates.nc /scratch/jelt/tmp/coordinates_AMM7.nc

  # e.g. 5 days of AMM60 data
  rsync -uartv jelt@login.archer.ac.uk:/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/OUTPUT/AMM60*nc /scratch/jelt/tmp/.
  scp jelt@login.archer.ac.uk:/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/WDIR/coordinates.nc /scratch/jelt/tmp/coordinates_AMM60.nc


which can be analysed on livljobs6.
Currently holding data on */scratch/jelt/tmp*. This is not a good place. It should move to */projectsa* at some point.

Some data exists on a USB drive for protyping on the move.
