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


FAIL. The entire contents of AMM60_1d_20120601_20120605_grid_W.nc have the same gridding problem. Perhaps the issues is in the XML grid definitions


Different grid are witten with the XIOS calls.
It appears that I am missing some important lines in diawri.F90. However Karen
made some edits there for pycnocline diagnostic, which need to be merged too.
For now, park Karen's version, copy in Maria's recompile and resubmit.
Replace diawri.F90 code::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/MY_SRC
  mv diawri.F90 diawri.F90_kariho40
  cp /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/NEMO/OPA_SRC/DIA/diawri.F90 diawri.F90_mane1
  ln -s diawri.F90_mane1 diawri.F90

Comment out diagnostic for top, middle, bottom thingy::

  vi diawri.F90
  ...
  !   USE diatmb          ! Top,middle,bottom output
  ...
  !      IF (ln_diatmb) THEN
  !         CALL dia_tmb
  !      ENDIF

Recompile etc ::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG
  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel

  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10 clean
  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10

  cd XIOS_AMM60_nemo_harmIT2/EXP_harmIT2
  vi run_counter.txt
    1 1 7200 20100105
    2 1264321 1271520

Resubmit::

  ./run_nemo
  4246757.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  4246757.sdb     jelt     standard AMM60_har2    --   92 220    --  00:25 Q   --

**Actions:**
* This might be a poor implementation of a good idea.
* Check the eps25h and tke25h output.
* Check S2_25h output which stores tmask. Is it the size I expect with sensible content?
* Output velocity with 25h averaging. This has a different grid.

cd /Volumes/archer/jelt//NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/OUTPUT

*(1 Feb 2017)* **MISSING INITIALISATION OF dia25h processes**

Data all duff. Same gridding issue. Reading into Python it looks like all the
data is 1e+20. This is the missing data value zmdi. And this includes all the
non-plotted stuff in FERRET
So perhaps the grid is not so bad it is the values that are the problem.

Read the data using ``AMM60_read_plot.ipynb``. This is probably more reliable the FERRET.
Inspection of the grid data and comparing with AMM7 and it looks OK.

grepping for 'dia_wri' in output logs shows that the dia25h.F90 was not entered.

grepping subroutine dia_25h_init is not called.

It *SHOULD* be called in nemogcm.F90

>    USE dia25h          ! 25h mean output
...
>    CALL dia_25h_init  ! 25h mean  outputs

Copy nemogcm.F90 into MY_SRC::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2
  cp ../../NEMO/OPA_SRC/nemogcm.F90 MY_SRC/.

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/MY_SRC
  diff nemogcm.F90 /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/NEMO/OPA_SRC/nemogcm.F90 | more

  vi nemogcm.F90
  ...
  USE dia25h
  ...
  IF(lwp) WRITE(numout,*) 'Euler time step switch is ', neuler
                        CALL dia_25h_init  ! 25h mean  outputs

Recompile etc ::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG
  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel

  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10 clean
  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10

  cd XIOS_AMM60_nemo_harmIT2/EXP_harmIT2
  vi run_counter.txt
    1 1 7200 20100105
    2 1264321 1271520

Resubmit::

  ./run_nemo
  4252750.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  4252750.sdb     jelt     standard AMM60_har2    --   92 220    --  00:25 Q   --

**Actions:**
* Check the eps25h and tke25h output.
* Check S2_25h output which stores tmask. Is it the size I expect with sensible content?
* Output velocity with 25h averaging. This has a different grid.
* When it works: replace Karen's diagIT in diawri.F90; restore output in dia25h.F90; fix other code bases; clean notes

cd /Volumes/archer/jelt//NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/OUTPUT
cd /work/n01/n01/jelt//NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/OUTPUT

*(2 Feb 2017)*

Reads in to namelist dia25 and then crashes

outputnamelist::

  &NAMTRD
LN_DYN_TRD      = F,
LN_KE_TRD       = F,
LN_VOR_TRD      = F,
LN_DYN_MXL      = F,
LN_TRA_TRD      = F,
LN_PE_TRD       = F,
LN_GLO_TRD      = F,
LN_TRA_MXL      = F,
NN_TRD  =         365
/
&NAM_DIA25H
LN_DIA25H       = T
/
*END OF FILE*

In the initialising phase of dia25
less ocean.output

dia_25h_init : Output 25 hour mean diagnostics
~~~~~~~~~~~~
Namelist nam_dia25h : set 25h outputs
Switch for 25h diagnostics (T) or not (F)  ln_dia25h  =  T
*END OF FILE*


Looking at an ocean.output file from an AMM7 run::

  less /work/n01/n01/jelt/from_mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/output_201202/ocean.output
  ..
  Euler time step switch is            1

   dia_tmb_init : Output Top, Middle, Bottom Diagnostics
   ~~~~~~~~~~~~
   Namelist nam_diatmb : set tmb outputs
   Switch for TMB diagnostics (T) or not (F)  ln_diatmb  =  F

   dia_25h_init : Output 25 hour mean diagnostics
   ~~~~~~~~~~~~
   Namelist nam_dia25h : set 25h outputs
   Switch for 25h diagnostics (T) or not (F)  ln_dia25h  =  T

  AAAAAAAA


   sbc_tide : Update of the components and (re)Init. the potential at kt=
             1
   ~~~~~~~~
   Q1   -0.190437839525882       0.961724601942270        11.1552706366618
    6.495854101908828E-005
   O1   -0.190437839525882       0.961724601942270        8.31628077515238
    6.759774402887834E-005
   P1    0.000000000000000E+000   1.00000000000000      -0.698071644152197
    7.252294578606445E-005
   S1    0.000000000000000E+000   1.00000000000000        3.15250096141476
    7.272205216643040E-005
   K1    0.152999615230591       0.976665375188633        7.00307356698171
    7.292115854679635E-005
   2N2   3.523888426978372E-002   1.01215178979009        20.9973340651529
    1.352404965560946E-004
   MU2   3.523888426978372E-002   1.01215178979009        24.3337067614387
    1.355937008184885E-004
   N2    3.523888426978372E-002   1.01215178979009        18.1583442036435
    1.378796995658846E-004
   NU2   3.523888426978372E-002   1.01215178979009        21.4947168999292
    1.382329038282786E-004
   M2    3.523888426978372E-002   1.01215178979009        15.3193543421341
    1.405189025756747E-004
   L2    3.523888426978372E-002   1.05497486424526        15.6219571342145
    1.431581055854647E-004
   T2    0.000000000000000E+000   1.00000000000000        5.82549023405499
    1.452450074605893E-004
   S2    0.000000000000000E+000   1.00000000000000        6.30500192282952
    1.454441043328608E-004
   K2    0.301179257684973       0.923664480285121        17.1477397875532
    1.458423170935927E-004
   M4    7.047776853956744E-002   1.02445124557527        30.6387086842682
    2.810378051513493E-004

   sbc_apr : Atmospheric pressure


The AMM60 is getting stuck before outputing::

    AAAAAAAA


     sbc_tide : Update of the components and (re)Init. the potential at kt=
               1

Add lots of comments to see where the break happens in dia25h.F90 and nemogcm.F90


Recompile etc ::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG
  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel

  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10 clean
  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10

  cd XIOS_AMM60_nemo_harmIT2/EXP_harmIT2
  vi run_counter.txt
    1 1 7200 20100105
    2 1264321 1271520

Shorten the run time from 25min::

  vi submit_nemo.pbs
  ...
  #PBS -l walltime=00:05:00

Resubmit::

  ./run_nemo
  4254220.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  4254220.sdb     jelt     standard AMM60_har2    --   92 220    --  00:05 Q   --


**It broke in CALL theta2t**

Add some Write statements into diainsitutem.F90, theta2t. Keep the queue short at 5mins.
e.g.::

  vi diainsitutem.F90
  ...
  IF(1) WRITE(6,*) 'jelt:theta2t: post lbc_lnk'


Recompile etc::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG
  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel

  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10 clean
  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10

  cd XIOS_AMM60_nemo_harmIT2/EXP_harmIT2
  vi run_counter.txt
    1 1 7200 20100105
    2 1264321 1271520

Resubmit::

  ./run_nemo
  4254832.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  4254832.sdb     jelt     standard AMM60_har2    --   92 220    --  00:05 Q   --

Problem happens in SUBROUTINE theta2t in CALL lbc_lnk( rinsitu_t,  'T', 1.0)
This is in lbclnk.F90, which is the same file in AMM60 and AMM7 code base.

The problem is in calculating boundary conditons on ``rinsitu_t``. I don't care about this
so comment it out::

  vi diainsitutem.F90
  ...
  SUBROUTINE theta2t
  ...
  ! jelt: 2 Feb 2017. comment CALL lbc_lnk out because the boundary code does not work with my code base.
  ! CALL lbc_lnk( rinsitu_t,  'T', 1.0)


Recompile::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG

  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10

  cd XIOS_AMM60_nemo_harmIT2/EXP_harmIT2
  vi run_counter.txt
    1 1 7200 20100105
    2 1264321 1271520

Resubmit::

  ./run_nemo
  4255022.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  4255022.sdb     jelt     standard AMM60_har2    --   92 220    --  00:05 Q   --



less ocean.output_EXP_harmIT2

  dia_25h_init : Output 25 hour mean diagnostics
~~~~~~~~~~~~
Namelist nam_dia25h : set 25h outputs
Switch for 25h diagnostics (T) or not (F)  ln_dia25h  =  T
jelt:dia_25h_init: allocated arrays
jelt:dia_25h_init: assignied initial values, 1
jelt:dia_25h_init: assignied initial values, 2


less stdouterr

...
jelt:theta2t: pre lbc_lnk
jelt:theta2t: post lbc_lnk
forrtl: severe (174): SIGSEGV, segmentation fault occurred
Image              PC                Routine            Line        Source
...

The problem seems to be with the ``rinsitu_t`` variable. Comment out all ``rinsitu_t(:,:,:)`` and ``CALL theta2t`` in
``dia25h.F90``
e.g.::

  vi dia25h.F90
  ...
  !CALL theta2t
  !rinsitu_t_25h(:,:,:) = rinsitu_t(:,:,:)



Recompile::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG

  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10

  cd XIOS_AMM60_nemo_harmIT2/EXP_harmIT2
  vi run_counter.txt
    1 1 7200 20100105
    2 1264321 1271520

Resubmit::

  ./run_nemo
  4255212.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  4255212.sdb     jelt     standard AMM60_har2    --   92 220    --  00:05 Q   --


**THIS WORKS!**

* Fix the walltime (25mins) in submit_nemo.pbs
* Remove all the jelt comments
* check run_counter.txt

Resubmit::

  ./run_nemo
  4255270.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  4255270.sdb     jelt     standard AMM60_har2    --   92 220    --  00:25 Q   --

Ran for 5:01
less time.step:   1265759

less stdouterr
> Error [CObjectFactory::GetObject(const StdString & id)] : In file '/work/n01/n01/jdha/ST/xios-1.0/src/object_factory_impl.hpp', line 79 -> [ id = avt25h, U = field ]  object is not referenced !

tail ocean.output_EXP_harmIT2
...
Horizontal mixing in s-coordinate: slope = slope of s-surfaces
Horizontal mixing in s-coordinate: slope = slope of s-surfaces
dia_wri_tide : Summing instantaneous hourly diagnostics at timestep
1265760
~~~~~~~~~~~~
dia_tide : Summed the following number of hourly values so far          25
dia_wri_tide : Writing 25 hour mean tide diagnostics at timestep     1265760
~~~~~~~~~~~~
dia_wri_tide : Mean calculated by dividing 25 hour sums and writing output


Comment out all the ``avt`` and ``avm`` terms in ``dia25h.F90``. Perhaps AVT
doesn't work because I dropping insitu teemperature??? Seems unlikely.
Anyway we don't need either of AVT or AVM as 25h diagnostics.

Recompile::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG

  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10

  cd XIOS_AMM60_nemo_harmIT2/EXP_harmIT2
  vi run_counter.txt
    1 1 7200 20100105
    2 1264321 1271520

Resubmit::

  ./run_nemo
  4255436.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  4255436.sdb     jelt     standard AMM60_har2    --   92 220    --  00:25 Q   --

**Actions:**
* Check the eps25h and tke25h output. (python)
* Check S2_25h output which stores tmask. Is it the size I expect with sensible content?
* Output velocity with 25h averaging. This has a different grid.
* When it works: replace Karen's diagIT in diawri.F90; restore output in dia25h.F90; fix other code bases; clean notes

cd /Volumes/archer/jelt//NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/OUTPUT
cd /work/n01/n01/jelt//NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/OUTPUT

----

*(3 Feb 2017)*
Run completed in 23mins.

**The 25h diagnostics all work**

* sshfs connection to archer
* livmaf: cd python/ipynb/jcomp_tools_dev/; jupyter notebook
* copy working grid_W.nc data to /scratch/jelt/tmp
* copied all the key output files to /scratch/jelt/tmp::

  cd /scratch/jelt/tmp
  rsync -uartv jelt@login.archer.ac.uk:/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/OUTPUT/AMM60*nc .
  rsync -uartv jelt@login.archer.ac.uk:/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/WDIR/coordinates.nc .

Clean ``nemogcm.F90`` of jelt write comments.
Tidy comments in ``dia25h.F90`` that avoid ``CALL theta2t`` and ``avm`` and
``avt`` 25h diagnostics. Avoid ``theta2t`` because it in turn has an MPI call
for boundaries ``CALL lbc_lnk`` that does not work with this (Karen's) code base.

Restore 25h diagnostics calculations to include the tmask (``dia25h.F90``).

Recompile::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG

  ./makenemo -n XIOS_AMM60_nemo_harmIT2 -m XC_ARCHER_INTEL -j 10

  cd XIOS_AMM60_nemo_harmIT2/EXP_harmIT2

5 days takes 25mins. 31 days estimate takes 155 = 2h 35min. Try 3hours (instead of 25mins)::

  vi submit_nemo.pbs
  ...
  #PBS -l walltime=03:00:00

  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520

  ./run_nemo
  4256984.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  4256984.sdb     jelt     standard AMM60_har2    --   92 220    --  03:00 Q   --


**Actions: PENDING 3Feb17**
* Check the eps25h, tke25h, S2_25h N2_25h output. (FERRET faster, or python: AMM60_read_plot.ipynb)
* When it works: restore Karen's diagIT in diawri.F90; clean notes
* Move data to SAN: ``rsync -uartv jelt@login.archer.ac.uk:/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/OUTPUT/AMM60*nc /scratch/jelt/tmp/.``
* Proceed with diagnostic analysis: `diagnostics_3D_harmonics<diagnostics_3D_harmonics.html>`_.

cd /Volumes/archer/jelt//NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/OUTPUT
cd /work/n01/n01/jelt//NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT2/EXP_harmIT2/OUTPUT
