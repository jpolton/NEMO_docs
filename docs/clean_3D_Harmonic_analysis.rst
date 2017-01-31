======================================
clean 3D Harmonic build and submission
======================================

clean build
===========

::

  chmod a+rx /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/MY_SRC/*

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG

  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel

  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10 clean

  mkdir XIOS_AMM60_nemo_harmIT2/MY_SRC
  cp XIOS_AMM60_nemo_harmIT/MY_SRC/* XIOS_AMM60_nemo_harmIT2/MY_SRC/.
  cp XIOS_AMM60_nemo_harmIT/cpp_XIOS_AMM60_nemo_harmIT.fcm XIOS_AMM60_nemo_harmIT2/cpp_XIOS_AMM60_nemo_harmIT2.fcm

  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10

----


Make new EXPeriment
===================

Make new experiment directory::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2
  mkdir EXP_harmIT2

Copy files but not directories::

  cp ../XIOS_AMM60_nemo_harmIT/EXP_harmIT/* ../XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/.

Link restart files::

  mkdir EXP_harmIT2/RESTART
  ln -s  /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/EXPD376/RESTART/01264320  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/RESTART/.

Edit run_counter,  to run for 5 days)::

  cd EXP_harmIT2
  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520

Edit submission script, and maybe the wall time::

  vi submit_nemo.pbs
  #PBS -N AMM60_har2
  #PBS -l walltime=00:25:00

Edit run file for new directory path::

  vi run_nemo
  export RUNNAME=EXP_harmIT2
  export HOMEDIR=/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2

Check code to edit the harmonic analysis terms in the namelist file::

  less run_nemo
  ...
  273c\
  nit000_han = "$nn_it000"
  274c\
  nitend_han = "$nitend" " $JOBDIR/namelist_cfg > $WDIR/namelist_cfg

Check ``iodef.xml`` file for harmonic output and 25hr output::

  less iodef.xml


Submit job::

  ./run_nemo
  4011945.sdb


| **Does it WORK? (27 Oct 2016)**
| **OUTPUT SHOULD BE 3D harmonics, for 5 days. Also various daily files.**

cd  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/

| run_counter.txt is added to without garbled date
| lots of files in OUTPUT
| Issue with GDEPW not being complete (perhaps it is defined twice?) The map works north of mid Wales, though is wrapped westward. Perhaps W-grid items have a dimension length problem?
| I think the problem is with the 25h data

**ACTION: CHECK field_def.xml DEFINITIONS FOR THE 25H VARIABLES**
Get on with: ``/Users/jeff/python/ipynb/NEMO/3DHarmonicView.ipynb`` (loads data on /Volumes/archer)


----

Make new M2 EXPeriment, with only M2 output
===========================================

Make new experiment directory::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2
  mkdir EXP_harmIT2

Copy files but not directories::

  cp ../XIOS_AMM60_nemo_harmIT/EXP_harmIT/* ../XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/.

Link restart files::

  mkdir EXP_harmIT2/RESTART
  ln -s  /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/EXPD376/RESTART/01264320  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/RESTART/.

Edit run_counter,  to run for 5 days)::

  cd EXP_harmIT2
  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520

Edit submission script, and maybe the wall time::

  vi submit_nemo.pbs
  #PBS -N AMM60_har2
  #PBS -l walltime=00:25:00
  #PBS -A n01-NOCL

Edit run file for new directory path::

  vi run_nemo
  export RUNNAME=EXP_harmIT2
  export HOMEDIR=/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2

Check code to edit the harmonic analysis terms in the namelist file::

  less run_nemo
  ...
  273c\
  nit000_han = "$nn_it000"
  274c\
  nitend_han = "$nitend" " $JOBDIR/namelist_cfg > $WDIR/namelist_cfg

Check ``iodef.xml`` file for harmonic output and 25hr output. Only output M2 (or it is too large to play with)::

  less iodef.xml


Submit job::

  ./run_nemo
  4109840.sdb


| **Does it WORK? (12 Dec 2016)**
| **OUTPUT SHOULD BE 3D harmonics, for 5 days. Also various daily files.**
| **This is identical to previous run (ABOVE) but with only M2 tidal species AND with new PBS account**

cd  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/

22 Dec 16
Seems OK but I am missing the e[12][uvt] files for AMM60. Add them into iodef.xml. Trim run_counter.txt and resubmit::

  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520

  vi iodef.xml
  <file id="file51" name_suffix="_grid_T" description="ocean T grid variables" >
      <field field_ref="e1t"  />
      <field field_ref="e2t"  />
      <field field_ref="e3t"  />
  ...
  <file id="file53" name_suffix="_grid_U" description="ocean U grid variables" >
        <field field_ref="e1u"  />
        <field field_ref="e2u"  />
        <field field_ref="e3u"  />
  ...
  <file id="file54" name_suffix="_grid_V" description="ocean V grid variables" >
          <field field_ref="e1v"  />
          <field field_ref="e2v"  />
          <field field_ref="e3v"  />
  ...

  ./run_nemo
  4126747.sdb


| **Does GRID it WORK? (21 Dec 2016)**
| **OUTPUT SHOULD BE 3D harmonics, for 5 days. Also various daily files.**
| **I don't think that the 25h data is working**
| **This is identical to previous run (ABOVE) but with e1u, e2u, e1v, e2v, e1t, e2t**

cd  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/

The horizontal grids didn't work. It looks like they are not properly defined.
At best the output is a depth variable. **Action** check field_def.xml::

  vi /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/SHARED/field_def.xml

  <field id="e1v"          long_name="V-cell width"     standard_name="cell_width"      unit="m"       grid_ref="grid_V_3D" />
  <field id="e2v"          long_name="V-cell depth"     standard_name="cell_depth"      unit="m"       grid_ref="grid_V_3D" />         <field id="e3v"          long_name="V-cell thickness" standard_name="cell_thickness"  unit="m"       grid_ref="grid_V_3D" />
  <field id="e3v"          long_name="V-cell thickness"     standard_name="cell_thickness"   unit="m"  grid_ref="grid_V_3D" />

  <field id="e1u"          long_name="U-cell width"     standard_name="cell_width"      unit="m"       grid_ref="grid_U_3D" />
  <field id="e2u"          long_name="U-cell depth"     standard_name="cell_depth"      unit="m"       grid_ref="grid_U_3D" />
  <field id="e3u"          long_name="U-cell thickness"     standard_name="cell_thickness"   unit="m"  grid_ref="grid_U_3D" />

  <field id="e1t"          long_name="T-cell width"   standard_name="cell_width"   unit="m"   grid_ref="grid_T_3D"/>
  <field id="e2t"          long_name="T-cell depth"   standard_name="cell_depth"   unit="m"   grid_ref="grid_T_3D"/>
  <field id="e3t"          long_name="T-cell thickness"   standard_name="cell_thickness"   unit="m"   grid_ref="grid_T_3D"/>

  ./run_nemo
  4128089.sdb


| **Does GRID it WORK? (23 Dec 2016)**
| **OUTPUT SHOULD BE 3D harmonics, for 5 days. Also various daily files.**
| **I don't think that the 25h data is working**
| **This is identical to previous run (ABOVE) but with e1u, e2u, e1v, e2v, e1t, e2t now defined**

cd  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/

Inspection of ``AMM60_1d_20120601_20120605_grid_W.nc``:
The GDEPW and EPS25H fields are gridded onto a subdomain. E3W is OK. Perhaps the problem is hard wired in the FORTRAN... Not that I can see.

Try copying Maria's AMM7 code with 3D harmonics and changing it to AMM60...

Logs at `AMM7 3D Harmonic analysis <AMM7_3D_Harmonic_analysis.html>`_

*(19 Jan 17)*
Copy in Maria's executable
Edit the link to the executable in the script ``run_nemo`` to point to the executable from Maria's code.
Actually the executable from Maria's source is also called ``nemo.exe``, residues in the build location
and is symbolically linked to the run directory. So here take the link to the build directory::

  cd  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/

  vi run_nemo
  ...
  rm  $JOBDIR/$EXEC
  #ln -s $CODEDIR/bin/$EXEC $JOBDIR/$EXEC
  ln -s /work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r7564_harm3d/NEMOGCM/CONFIG/XIOS_AMM60_nemo/BLD/bin/nemo.exe $JOBDIR/$EXEC

Trim ``run_counter.txt::

  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520


Submit job::

    ./run_nemo
    4200546.sdb

| **Does GRID it WORK? (19 Jan 2017)**
| **OUTPUT SHOULD BE 3D harmonics, for 5 days. Also various 25h files.**


Simulation crashes instantly because ``nb_jpk_bdy`` is not defined in the FORTRAN.
This variable declares the number of vertical levels in the boundary forcing data, if different from the config domain.
Maria's code, and recent checkouts (r7564), does not have the option to vertically interpolate bdy grid and so does not have this variable.

However Fred's code does have this (as did Karen's). I will recompile Maria's code with Fred's bdyini.F90. He also shared a bdydyn3d.F90 which I may as well also use.
Notes on recompiling source code are at `AMM7 3D Harmonic analysis<AMM7_3D_Harmonic_analysis.html>`_

However another variable ``sponge_factor`` is also affected by code updates. Karen used the sponge but Maria didn't.
If I comment out the line regarding the ``sponge_factor`` then the model compiles. Fixing ``sponge_factor`` will require modifications to other source files.
Ensure that the sponge is not evoke in the namelist::

  vi namelist_cfg
  ln_sponge = .false.

Resubmit::

  ./run_nemo
  4200654.sdb

----

*(27 Jan 2017)*
Decided to revert back to old executable, which basically worked. And find an
alternative way to output dissipation. Here the working 3D harmonics are restored.
Next steps to output dissipation and any additional necessary grid information.

Recompile::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG
  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel

  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10 clean
  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10

Ensure the executable is correct::

    vi run_nemo
    ...
    ln -s $CODEDIR/bin/$EXEC $JOBDIR/$EXEC

Copy namelists back in::

  cp ../XIOS_AMM60_nemo_harmIT/EXP_harmIT/namelist* ../XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/.

Comment out 25h output in iodef.xml::

  vi iodef.xml
  /25h

Resubmit::

  ./run_nemo
  4215689.sdb


----

*(30 Jan 2017)*

Got output. Check it in Ferret::

  cd /Volumes/archer/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/OUTPUT

Load in Tides looks ok.

Load T-grid output:
* E3T **OK**
* E1T and E2T **BAD** grid messed up.

Load in velocity output (u-vel):
* E3U, UO, UBAR, UOS **OK**
* E1U, E2U, GDEPU **BAD** grid messed up.

Talking to Maria, she pointed out that e[12][uvt] are not needed in the ``iodef.xml``
file since they don't change. Instead get them from ``coordinates.nc``.

Noted that the *bad* horizontal grid files are 2d but were written as if they
were 3d. This could have been why the gridding was garbled.

The tke25h and eps25h have had grids that are messed up in a similar way. Perhaps
they were written in a way that creates conflicts with the dimensions.

----

*(31 Jan 17)*

Repeat the above process. Recompile code::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG
  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel

  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10 clean
  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10

  cd XIOS_AMM60_nemo_harmIT2

Trim run_counter.txt to two lines::

  vi EXP_harmIT2/run_counter.txt


Check namelists. Should be OK. (These are read and editted from the JOBDIR)

Edit the iodef.xml file. Output 1h SSH. Only M2x_SSH, M2x_rho, tke_25h, eps_25h.
Remove all the horiztonal grid spacings as these can come from coordinates.nc

Diff the iodef.xml with AMM7 version (that works)::

  diff iodef.xml /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/iodef.xml

Looks ok on quick inspection.
Submit::

    ./run_nemo
    4245219.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  4245219.sdb     jelt     standard AMM60_har2    --   92 220    --  00:25 Q   --


* If there are problems, perhaps also diff the other XML files. Check the namelist differences.

Run Completed.

cd /Volumes/archer/jelt//NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/OUTPUT

All variables in AMM60_1d_20120601_20120605_Tides.nc OK
currently SET data sets:
1> ./AMM60_1d_20120601_20120605_Tides.nc  (default)
name     title                             I         J         K         L         M         N
NAV_LAT  Latitude                         1:1120    1:1440    ...       ...       ...       ...
NAV_LON  Longitude                        1:1120    1:1440    ...       ...       ...       ...
E3T      T-cell thickness                 1:1120    1:1440    1:51      1:5       ...       ...
TIME_CENTERED
     Time axis                        ...       ...       ...       1:5       ...       ...
TIME_CENTERED_BOUNDS
                                      1:2       ...       ...       1:5       ...       ...
GDEPT    depth                            1:1120    1:1440    1:51      1:5       ...       ...
M2X_RO   M2 ro   real part                1:1120    1:1440    1:51      ...       ...       ...
M2X_SSH  M2 ro   real part                1:1120    1:1440    ...       ...       ...       ...

All OK.

AMM60_1h_20120601_20120605_SSH.nc is OK

MM60_1d_20120601_20120605_grid_T.nc
 name     title                             I         J         K         L         M         N
 E3T      T-cell thickness                 1:1120    1:1440    1:51      1:5       ...       ...
 ...
 GDEPT    depth                            1:1120    1:1440    1:51      1:5       ...       ...

E3T OK
GDEPT NOT OK. Gridding is wrong (as before)

AMM60_1d_20120601_20120605_grid_W.nc
name     title                             I         J         K         L         M         N
E3W      W-cell thickness                 1:1120    1:1440    1:51      1:5       ...       ...
...
GDEPW    W-cell depth                     1:1120    1:1440    1:51      1:5       ...       ...
TKE25H   25h vertical kinetic energy      1:1120    1:1440    1:51      1:5       ...       ...

E3W OK
GDEPW, TKE25H NOT OK. Gridding is wrong (as before)

Looking through the MY_SRC files perhaps the GDEP? variables are not actually defined properly and so should not be used.

Add (:,:,:) to zw3d in writing statement (diaharm.F90 has these)
cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/MY_SRC
 vi dia25h.F90
           CALL iom_put("tke25h", zw3d(:,:,:))   ! tke


Recompile add tke25h and eps25h to iodef.xml to see if there is a difference.
Trim run_counter.txt
Edit iodef.xml (as suggested). Add tke25g and eps25h, remove gdep*
Resubmit::

  ./run_nemo
  4245537.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  4245537.sdb     jelt     standard AMM60_har2    --   92 220    --  00:25 Q   --



* try and output tmask to XIOS control
* comment out masks in 25h outputs
* output all 25h outputs.
* Do I need zdftke.F90. There are differences in the definition of ``en``. Maria say's no, I don't use or need key_zdftke

RUN COMPETED SAME PROBLEM WITH BOTH eps25h and tke25h. Therefore it was insufficient to edit the Fortran::

  vi dia25h.F90
  ...
            CALL iom_put("tke25h", zw3d(:,:,:))   ! tke
From::

            CALL iom_put("tke25h", zw3d)   ! tke

Proceeding,
Comment out the masks in the definitions of tke25h etc::

  vi dia25h.F90
  ...

  #if defined key_zdftke || defined key_zdfgls
             zw3d(:,:,:) = en_25h(:,:,:)*tmask(:,:,:) + zmdi*(1.0-tmask(:,:,:))
             CALL iom_put("tke25h", zw3d(:,:,:))   ! tke
  #endif
  #if defined key_zdfgls
             zw3d(:,:,:) = rmxln_25h(:,:,:)*tmask(:,:,:) + zmdi*(1.0-tmask(:,:,:))
             CALL iom_put( "mxln25h",zw3d)
             zw3d(:,:,:) = eps_25h(:,:,:)*tmask(:,:,:) + zmdi*(1.0-tmask(:,:,:))
             CALL iom_put( "eps25h",zw3d)
             zw3d(:,:,:) = N2_25h(:,:,:)*tmask(:,:,:) + zmdi*(1.0-tmask(:,:,:))
             CALL iom_put( "N2_25h",zw3d)
             zw3d(:,:,:) = S2_25h(:,:,:)*tmask(:,:,:) + zmdi*(1.0-tmask(:,:,:))
             CALL iom_put( "S2_25h",zw3d)

  #endif


Becomes::

  vi dia25h.F90
  ...

  #if defined key_zdftke || defined key_zdfgls
             zw3d(:,:,:) = en_25h(:,:,:) ! *tmask(:,:,:) + zmdi*(1.0-tmask(:,:,:))
             CALL iom_put("tke25h", zw3d(:,:,:))   ! tke
  #endif
  #if defined key_zdfgls
             zw3d(:,:,:) = rmxln_25h(:,:,:) ! *tmask(:,:,:) + zmdi*(1.0-tmask(:,:,:))
             CALL iom_put( "mxln25h",zw3d)
             zw3d(:,:,:) = eps_25h(:,:,:) ! *tmask(:,:,:) + zmdi*(1.0-tmask(:,:,:))
             CALL iom_put( "eps25h",zw3d)
             zw3d(:,:,:) = N2_25h(:,:,:) ! *tmask(:,:,:) + zmdi*(1.0-tmask(:,:,:))
             CALL iom_put( "N2_25h",zw3d)
             zw3d(:,:,:) = S2_25h(:,:,:) ! *tmask(:,:,:) + zmdi*(1.0-tmask(:,:,:))
             !jelt !CALL iom_put( "S2_25h",zw3d)
             CALL iom_put( "S2_25h",tmask)

  #endif

Add in N2 and S2 25h fields to iodef.xml file since they have different masks,
well actually N2, S2 have the same mask as the tke and eps fields.
However I have overwritten the S2_25h field with tmask variable.

Trim ``run_counter.txt`` to two lines.

Recompile as above.

Resubmit::

  ./run_nemo
  4246394.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  4246394.sdb     jelt     standard AMM60_har2    --   92 220    --  00:25 Q   --

**Actions:**
* Check the eps25h and tke25h output.
* Check S2_25h output which stores tmask. Is it the size I expect with sensible content?
* Output velocit with 25h averaging. This has a different grid.
