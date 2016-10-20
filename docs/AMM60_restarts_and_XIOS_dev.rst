===========================
AMM60 restarts and XIOS dev
===========================

Aim to pick up and old 2012 restart file and run with new iodef.xml output.

Copy the restart file from Karens run. Perhaps  ``/work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/EXPD376``

In particular I want to rerun the June 2012 period (when there was a cruise) and output lots of virtual moorings for Jo Hopkins’ student (David) to analyse the shelf break processes.


PLAN:

#. Create a clean experiment directory
#. Clean up the iodef.xml file
#. Test run a few days
#. Expand the iodef.xml file
#. Do one month simulation

NOTE I DO NOT KNOW THE DATE OF THE RESTART: Update  1271520=20120605
1264321 Seems to correspond to midnight 1 June 2012 (plus or minus 30 mins)

----

Clean experiment directory, EXP_SBmoorings
==========================================

Follow notes from `Testing a new compilation with NSea experiment <Testing_a_new_compilation_with_NSea_experiment.html>`_::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo
  mkdir EXP_SBmoorings

Copy run script into job directory. Note this is not necessary as all the paths are set in the script but is means that each experiment can have its own run script::

  cp EXP_NSea/run_nemo EXP_SBmoorings/run_nemo
  cp EXP_NSea/submit_nemo.pbs EXP_SBmoorings/submit_nemo.pbs

Edit run script (Note for only one restart ``restart_max=2``)::

  vi EXP_SBmoorings/run_nemo
  export RUNNAME=EXP_SBmoorings
  export YEARrun='2012'
  export HOMEDIR=/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo
  export nrestart_max=7  (For one submission this number must equal the number of lines in run_counter.txt)

  vi EXP_SBmoorings/submit_nemo.pbs
  #PBS -N AMM60_SB
  #PBS -l walltime=00:20:00
  ..
    $JOBDIR/finish_nemo.sh
    $JOBDIR/run_nemo
  ..

Copy ``iodef.xml`` into job directory::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_notradiff_long/iodef_fastnet.xml EXP_SBmoorings/iodef_sbmoorings.xml
  vi iodef_sbmoorings.xml (E.g. for testing)
      <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->
  ...
          <file id="file001" name_suffix="_SB001_grid_T" description="ocean T grid variables">
            <field_group id="1h_SB001_grid_T" domain_ref="SB001" >
              <field field_ref="toce"      name="thetao"  long_name="sea water potential temperature" />
              <field field_ref="soce"      name="so"      long_name="sea water salinity"              />
              <field field_ref="tpt_dep"      name="depth"  />
              <field field_ref="uoce"      name="vozocrtx" long_name="sea water x velocity" />
              <field field_ref="voce"      name="vomecrty" long_name="sea water y velocity" />
              <field field_ref="woce"      name="vovecrtz" long_name="sea water w velocity" />
              <field field_ref="utau"      name="utau"  long_name="surface downward x stress" />
              <field field_ref="vtau"      name="vtau"  long_name="surface downward y stress" />
              <field field_ref="ssh"          name="ssh"      long_name="sea surface height"/>
            </field_group>
          </file>

        </file_group>

Copy ``domain_def.xml`` into job directory::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_NSea/domain_def.xml EXP_SBmoorings/domain_def.xml

Edit XML files to contain virtual moorings at given lat/lon locations from supplied list. Use python notebook to generate text to paste into XML files:
``jcomp_tools_dev/AMM60_build_iodef_from_latlon.ipynb``::

  vi domain_def.xml (E.g. for testing)

        <!-- Shelf Break virtual moorings -->
        <domain id="SB032" zoom_ibegin="0610" zoom_jbegin="0302" zoom_ni="2" zoom_nj="2" />
        <domain id="SB001" zoom_ibegin="0608" zoom_jbegin="0304" zoom_ni="2" zoom_nj="2" />
        <domain id="SB002" zoom_ibegin="0607" zoom_jbegin="0306" zoom_ni="2" zoom_nj="2" />
        <domain id="SB003" zoom_ibegin="0605" zoom_jbegin="0307" zoom_ni="2" zoom_nj="2" />
        <domain id="SB004" zoom_ibegin="0604" zoom_jbegin="0309" zoom_ni="2" zoom_nj="2" />
        <domain id="SB005" zoom_ibegin="0603" zoom_jbegin="0311" zoom_ni="2" zoom_nj="2" />
        <domain id="SB006" zoom_ibegin="0601" zoom_jbegin="0312" zoom_ni="2" zoom_nj="2" />
        <domain id="SB007" zoom_ibegin="0600" zoom_jbegin="0314" zoom_ni="2" zoom_nj="2" />
        <domain id="SB008" zoom_ibegin="0599" zoom_jbegin="0316" zoom_ni="2" zoom_nj="2" />
        <domain id="SB009" zoom_ibegin="0597" zoom_jbegin="0317" zoom_ni="2" zoom_nj="2" />
        <domain id="SB010" zoom_ibegin="0596" zoom_jbegin="0319" zoom_ni="2" zoom_nj="2" />
        <domain id="SB011" zoom_ibegin="0595" zoom_jbegin="0321" zoom_ni="2" zoom_nj="2" />
        <domain id="SB012" zoom_ibegin="0593" zoom_jbegin="0322" zoom_ni="2" zoom_nj="2" />
        <domain id="SB013" zoom_ibegin="0592" zoom_jbegin="0324" zoom_ni="2" zoom_nj="2" />
        <domain id="SB014" zoom_ibegin="0590" zoom_jbegin="0325" zoom_ni="2" zoom_nj="2" />
        <domain id="SB015" zoom_ibegin="0588" zoom_jbegin="0327" zoom_ni="2" zoom_nj="2" />
        <domain id="SB016" zoom_ibegin="0621" zoom_jbegin="0319" zoom_ni="2" zoom_nj="2" />
        <domain id="SB017" zoom_ibegin="0620" zoom_jbegin="0321" zoom_ni="2" zoom_nj="2" />
        <domain id="SB018" zoom_ibegin="0618" zoom_jbegin="0322" zoom_ni="2" zoom_nj="2" />
        <domain id="SB019" zoom_ibegin="0617" zoom_jbegin="0324" zoom_ni="2" zoom_nj="2" />
        <domain id="SB020" zoom_ibegin="0615" zoom_jbegin="0326" zoom_ni="2" zoom_nj="2" />
        <domain id="SB021" zoom_ibegin="0614" zoom_jbegin="0327" zoom_ni="2" zoom_nj="2" />
        <domain id="SB022" zoom_ibegin="0613" zoom_jbegin="0329" zoom_ni="2" zoom_nj="2" />
        <domain id="SB023" zoom_ibegin="0611" zoom_jbegin="0330" zoom_ni="2" zoom_nj="2" />
        <domain id="SB024" zoom_ibegin="0610" zoom_jbegin="0332" zoom_ni="2" zoom_nj="2" />
        <domain id="SB025" zoom_ibegin="0609" zoom_jbegin="0334" zoom_ni="2" zoom_nj="2" />
        <domain id="SB026" zoom_ibegin="0607" zoom_jbegin="0336" zoom_ni="2" zoom_nj="2" />
        <domain id="SB027" zoom_ibegin="0606" zoom_jbegin="0338" zoom_ni="2" zoom_nj="2" />
        <domain id="SB028" zoom_ibegin="0605" zoom_jbegin="0339" zoom_ni="2" zoom_nj="2" />
        <domain id="SB029" zoom_ibegin="0603" zoom_jbegin="0341" zoom_ni="2" zoom_nj="2" />
        <domain id="SB030" zoom_ibegin="0602" zoom_jbegin="0342" zoom_ni="2" zoom_nj="2" />
        <domain id="SB031" zoom_ibegin="0600" zoom_jbegin="0344" zoom_ni="2" zoom_nj="2" />

When happy overwrite the ``iodef.xml`` with the new version::
  cp EXP_SBmoorings/iodef_sbmoorings.xml EXP_SBmoorings/iodef.xml

Copy ``finish_nemo.sh`` into job directory::
  cp /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_NSea/finish_nemo.sh EXP_SBmoorings/finish_nemo.sh

Link restart files::

  mkdir EXP_SBmoorings/RESTART
  ln -s  /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/EXPD376/RESTART/01264320  EXP_SBmoorings/RESTART/.

Create ``run_counter.txt`` into job directory (I don’t know the dates. NB Karen’s numbers are quite large but I don’t see the restart files). Note that the last line 2nd number must be +1 of the restart directory name. BEWARE of extra white spaces in these lines as the ‘cutting'  will not work properly with them
Edit ``run_counter.txt: 1264321`` is 1st June 2012, or perhaps ``20120531`` as this appears in the output file::

     Chopped from  AMM60_SB.o3960041
          01271520
              date ndastp                                      :     20120531

Anyway edit ``run_counter.txt`` to start at the beginning on June 2012::

  vi EXP_SBmoorings/run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520

Copy in namelists::

  cp EXP_NSea/namelist_ref EXP_SBmoorings/.
  cp EXP_NSea/namelist_cfg EXP_SBmoorings/.

Submit run::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings
  ./run_nemo
  3971205.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  3971205.sdb     jelt     standard AMM60_SB      --   92 220    --  00:20 Q   — <— IN PROGRESS. CAREFUL WALL TIME MAY BE EXCEEDED BY LARGE NUMBERS OF OUTPUT FILES (3305 moorings).

**IT BROKE.
TRY JUST ONE SUBMISSION TO DEBUG. ALSO cut down the number of output files in iodef.xml.**

::

  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520

  vi run_nemo
  export nrestart_max=2 #31 (For one submission this number must equal the number of lines in run_counter.txt)

Shorten the queue to get this thing going (hopefully)::

  vi submit_nemo.pbs
  #PBS -l walltime=00:01:00

Clean up a bit::

  rm -r OUTPUT/ WDIR/ LOGS/

| Cut down the number of XML output files to file000 - file999 in ``iodef.xml``.
| Original list is in ``iodef_sbmoorings.xml``
| If this work I will need to run the month 3 times to simulate 3305 moorings.

Submit run::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings
  ./run_nemo
  3972357.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  3972357.sdb     jelt     standard AMM60_SB      --   92 220    --  00:01 Q   --

| **Broke**. Looks like it didn’t like the ``iodef.xml`` file
| Save ``iodef.xml`` with 1000 virtual moorings as ``iodef_sbmoorings_000_999.xml``

Recover simple ``iodef.xml`` file from ANChor run::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_NSea/iodef.xml   iodef.xml

Clean it up and fix it to have nothing but the following::

      <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->
        <file id="file011" name_suffix="_SB011_grid_T" description="ocean T grid variables">
          <field_group id="1h_SB011_grid_T" domain_ref="SB011" >
            <field field_ref="toce"       name="thetao"   long_name="sea water potential temperature" />
            <field field_ref="soce"       name="so"       long_name="sea water salinity"              />
            <field field_ref="tpt_dep"      name="depth"   />
            <field field_ref="uoce"       name="vozocrtx" long_name="sea water x velocity" />
            <field field_ref="voce"       name="vomecrty" long_name="sea water y velocity" />
            <field field_ref="woce"       name="vovecrtz" long_name="sea water w velocity" />
            <field field_ref="utau"       name="utau"  long_name="surface downward x stress" />
            <field field_ref="vtau"       name="vtau"  long_name="surface downward y stress" />
            <field field_ref="ssh"          name="ssh"      long_name="sea surface height"/>
          </field_group>
        </file>
      </file_group>

Submit run with 20min wall time::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings
  ./run_nemo
  3972636.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  3972636.sdb     jelt     standard AMM60_SB      --   92 220    --  00:20 Q   —

**PENDING. Does it produce output?**
Yes, ``AMM60_1h_20120601_20120605_SB011_grid_T.nc`` exists. It is running now (15:55, 4 Oct 2016)


Yes.
Spotted error in the iodef_sbmooring*.xml files. Double definition of the 1h file_group without closing it.
``<file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->``

| Saved the working test iodef files: ``iodef_1mooring.xml``
| Copied the full file to the operational iodef file: ``cp iodef_sbmoorings_001_3305.xml iodef.xml``

Trim ``run_counter.txt``

Resubmit::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings
  ./run_nemo
  3977817.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  3977817.sdb     jelt     standard AMM60_SB      --   92 220    --  00:20 Q   --

| **PENDING. Does it produce mooring output?**
| CAREFUL WALL TIME MAY BE EXCEEDED BY LARGE NUMBERS OF OUTPUT FILES (3305 moorings). 7 Oct 201

::

  EXP_SBmoorings/LOGS/01271520> less stdouterr
  -> report :  Memory report : Context <nemo> : client side : total memory used for buffer 0 bytes


| Try and rewrite the XML output to all be in one file.
| Create a separate lookup for lat and lon.
| Save new file as ``iodef_1file.xml``

Create new GitHub repo: https://github.com/jpolton/EXP_SBmoorings

| Trim ``run_counter.txt``
| ``cp iodef_1file.xml iodef.xml``
| Check the 20min queue

Resubmit::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings
  ./run_nemo
  3982808.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  3982808.sdb     jelt     standard AMM60_SB      --   92 220    --  00:20 Q   —

| **<— PENDING. Does it produce mooring output?**
| CAREFUL WALL TIME MAY BE EXCEEDED BY LARGE NUMBERS OF VARIABLES in  FILE (3305 moorings). 10 Oct 2016

----

**11 Oct**

It runs and adds to ``run_counter.txt``::

  1 1 7200 20100105
  2 1264321 1271520
  3 1271521 1278720 1271520=20120605

Though OUTPUT contains no new files. (Though it should all go in one file now)::

  module load cray-netcdf
  ncdump -h OUTPUT/*nc

  ``time.step: 1271520`` -- indicates the run properly finished integration

  less AMM60_SB.o3982808 -- likewise shows wall time was not exceeded

  cd EXP_SBmoorings/LOGS/01271520
  less time-step ocean.output_EXP_SBmoorings

| Some warnings but no errors.
| Presumably a problem with the ``iodef.xml`` file

**Action:** Check the ``iodef.xml`` file

Copy ``iodef.xml`` to give a local file for inspection::

  cp iodef.xml ~/Desktop/OneWeekExpiry/.

Cut the file down to just a few field_group entries.
Resubmit::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings
  ./run_nemo
  3985580.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  3985580.sdb     jelt     standard AMM60_SB      --   92 220    --  00:20 Q   --

| **<— PENDING. Does it produce mooring output? (11 Oct 2016)**
| EXPECT A SINGLE MOORING *.nc FILE. WITH A NUMBER OR MOORINGS WITHIN.

----

**12 Oct 2016**

| It ran ``run_counter.txt`` has next step ready
| One nc output file, which is old. So no new XML output!
| Finished fine in 16mins. No walltime problem
| Nothing wrong in ``LOGS/01271520/ocean.output_EXP_SBmoorings``
| ``iodef.xml`` file is OK.

| **Action** Need to debug XML file in AMM7, on the short queue.

----

Spotting a spurious quote mark **"** at the end of ``file_group`` definition::

  <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->"

Try the whole lot in one go::

  cp iodef_1file.xml  iodef.xml

| Trim ``run_counter.txt``
| Check 20min queue


Resubmit::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings
  ./run_nemo
  3996749.sdb

Meanwhile remove spurious quotation mark " in ``iodef_sbmoorings_001_3305.xml`` and ``iodef_sbmoorings_001_999.xml``

**PENDING (18 Oct 2016)**

NO NETCDF OUTPUT. Needs further investigation.

Does it work with two moorings?::

  cp iodef_2moorings.xml  iodef.xml

| Trim ``run_counter.txt``
| Check 20min queue

Resubmit::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings
  ./run_nemo
  3998516.sdb

IT WORKS : 5 days came in at 20 mins, with two moorings. Nearly hit walltime. Renamed OUTPUT/AMM60*nc file to ..SB2.nc, or something similar.

Next step try 1000 moorings...

cp iodef_sbmoorings_001_999.xml  iodef.xml

| Trim ``run_counter.txt``
| Check 20min queue

Resubmit::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings
  ./run_nemo
  3999305.sdb


**PENDING (19 Oct 2016)** COMPLETED late, 10pm. Need to look at data.

* Is there lots of mooring output in a single file? ``AMM60_1h_20120601_20120605_SB_grid_T.nc`` is created. Wall time exceeded. 27 hours completed.
* Is the output from the prevous run, with two moorings, OK? - These data have 5 days of data
* Could try a few files with multiple moorings in each, say 33 files 100 moorings?

----

cp iodef_sbmoorings_33files.xml iodef.xml

| Trim ``run_counter.txt``. Not needed
| Check 20min queue. OK

Resubmit::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings
  ./run_nemo
  3999961.sdb

**PENDING (19 Oct 2016)**

* Are there 33 files of 100 moorings?
* Is the data OK?
* How is the data in the ``iodef_sbmoorings_001_999.xml`` simulation?

| There are NO XML outputs
| Nasty garbled run_counter.txt data, though it looks like it finished as a 3rd line is added
| Nothing new in ``OUTPUT``
| Ran for 10 mins (20min wall time)

Disparity between ``time.step`` and ``run_counter.txt``::

  more WDIR/time.step
  1265827

  more run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520
  3 1271521 1278720 _:U()I.=

Check LOGS::

  more stdouterr
  [NID 04371] 2016-10-19 22:21:27 Apid 23764253: initiated application termination
  [NID 04371] 2016-10-19 22:21:28 Apid 23764253: OOM killer terminated this process.
  Application 23764253 exit signals: Killed
  Application 23764253 resources: utime ~0s, stime ~68s, Rss ~4848, inblocks ~4163, outblocks ~166

  Out of memory:
  http://www.nersc.gov/users/computational-systems/retired-systems/hopper/running-jobs/memory-considerations/
  Recommend use fewer processors on each node, and therefor more nodes


How for did the run get? *25.1 hours* Surely the output should have started appearing?

----

Found bug in ``iodef_sbmoorings_33files.xml``: the variables names were not unique in the files. Fixed this.
Resubmit with more memory on the XIOS nodes.

cp iodef_sbmoorings_33files.xml iodef.xml



**Configure AMM60 SBmoorings to run on more XIOS nodes**

----

Standard::

  submit_nemo.pbs:
  #PBS -l select=92
  export NEMOproc=2000
  export XIOSproc=40
  aprun -b -n $NEMOproc -N 24 ./$EXEC : -N 5 -n $XIOSproc ./xios_server.exe >&stdouterr

  vi run_nemo
  export NPROC=2000

  Sums:
  NEMO nodes: ceil(2000 / 24) = 84
  XIOS nodes: ceil(40 / 5) = 8
  Total = 92

New Double XIOS nodes::

  submit_nemo.pbs:
  #PBS -l select=**100**
  export NEMOproc=2000
  export XIOSproc=**80**
  aprun -b -n $NEMOproc -N 24 ./$EXEC : -N 5 -n $XIOSproc ./xios_server.exe >&stdouterr

  vi run_nemo
  export NPROC=2000

  Sums:
  NEMO nodes: ceil(2000 / 24) = 84
  XIOS nodes: ceil(80 / 5) = 16
  Total = 100

Trim ``run_counter.txt``

Resubmit::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings
  ./run_nemo
  4000861.sdb

**PENDING (20 Oct 2016)**

* Are there 33 files of 100 moorings?
* How is the speed up with twice as many XIOS processors?
