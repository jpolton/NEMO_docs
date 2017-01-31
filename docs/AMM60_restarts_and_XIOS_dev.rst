===========================
AMM60 restarts and XIOS dev
===========================

Aim to pick up and old 2012 restart file and run with new iodef.xml output.

Copy the restart file from Karens run. Perhaps  ``/work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/EXPD376``

In particular I want to rerun the June 2012 period (when there was a cruise) and output lots of virtual moorings for Jo Hopkins’ student (David) to analyse the shelf break processes.

NOTE:

Outputting 3305 moorings using XIOS 1d techniques failed. Tried distributing moorings across different numbers of files.
Tried varying the processor allocation to XIOS.
There were either walltime issues or memory problems. This might be fixed in xios-2...
In the end outputted the data as a masked 3D spatial array, though not a elegant it was fast and worked!


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

Something Broke. Looks like a problem with the file naming, though file011 has previously worked
Edit 34 file names to be called file101 - file134.
These edits are in ``iodef.xml`` and not in ``iodef_sbmoorings_33files.xml``.

Not sure what to do! Need to download logs.

Looking at the logs:

| 6MB file created: AMM60_1h_20120601_20120605_SB001_grid_T.nc --> Can not read with ncdump or ferret. HDF error.
| run_counter.txt gets extra line though the data string is garbled.
| ran for 7 mins
| core dump
| time.step : 1264632 --> 5 hours of model time integration

::

  less LOGS/restart/stdouterr

  terminate called after throwing an instance of 'terminate called after throwing an instance of 'xios::CNetCdfExceptionxios::CNetCdfException'
  '
    what():  Error in calling function nc_enddef(ncId)
  NetCDF: HDF error
  Unable to end define mode of this file, given its id : 65536

  terminate called after throwing an instance of 'xios::CNetCdfException'
    what():  Error in calling function nc_enddef(ncId)
  NetCDF: HDF error
  Unable to end define mode of this file, given its id : 65536

  forrtl: error (76): Abort trap signal

This seems consistent with running out of memory.


**Try new configuration of XIOS processors**

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
  #PBS -l select=**104**
  export NEMOproc=2000
  export XIOSproc=**60**
  aprun -b -n $NEMOproc -N 24 ./$EXEC : -N **3** -n $XIOSproc ./xios_server.exe >&stdouterr

  vi run_nemo
  export NPROC=2000

  Sums:
  NEMO nodes: ceil(2000 / 24) = 84
  XIOS nodes: ceil(60 / 3) = 20
  Total = 104

| trim ``run_counter.txt``

Resubmit (Note I killed the original submission to fix the namelist_cfg sed edit issue with ``nn_write`` not be changed)::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings
  ./run_nemo
  4003469.sdb

**PENDING (21 Oct 2016)**

* Are there 33 files of 100 moorings?
* How is the speed up with twice as many XIOS processors?

| ``run_counter.txt`` with new line but garbled date
| ``OUTPUT/AMM60_1h_20120601_20120605_SB001_grid_T.nc`` exists but can not be read by either ncdump or FERRET.
| ``less time.step: 1264692``
| wall time of 6mins before terminating

LOGS/restart::

  vi stdouterr

    terminate called after throwing an instance of 'xios::CNetCdfException'
    what():  Error in calling function nc_enddef(ncId)
  NetCDF: HDF error
  Unable to end define mode of this file, given its id : 65536

  terminate called after throwing an instance of 'terminate called after throwing an instance of 'xios::CNetCdfExceptionxios::CNetCdfException'
  '
    what():  Error in calling function nc_enddef(ncId)
  NetCDF: HDF error
  Unable to end define mode of this file, given its id : 65536

    what():  Error in calling function nc_enddef(ncId)
  NetCDF: HDF error
  Unable to end define mode of this file, given its id : 65536

  forrtl: error (76): Abort trap signal
  Image              PC                Routine            Line        Source
  xios_server.exe    0000000000CB57E1  Unknown               Unknown  Unknown
  xios_server.exe    0000000000CB3F37  Unknown               Unknown  Unknown
  ...

----

Second Run - debugging XML.
================================

Make new EXPeriment::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/
  mkdir EXP_SBmoorings2

Copy files but not directories::

  cp EXP_SBmoorings/* EXP_SBmoorings2/.

Link restart files::

  mkdir EXP_SBmoorings2/RESTART
  ln -s  /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/EXPD376/RESTART/01264320  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings2/RESTART/.

Edit run_counter (run for 24 hours)::

  cd EXP_SBmoorings2
  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1265760


Edit submission script, and maybe the wall time::

  vi submit_nemo.pbs
  #PBS -N AMM60_SB2
  #PBS -l walltime=00:20:00

Edit run file for new directory path::

  vi run_nemo
  export RUNNAME=EXP_SBmoorings2

Edit ``iodef.xml`` file to have 100 moorings in one file and 5 in the second (last) file::

  less iodef_sbmoorings_100moorings_2files.xml

  <!-- Shelf Break virtual moorings -->
      <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->
        <file id="file001" name_suffix="_SB001_grid_T" description="ocean T grid variables">
          <field_group id="1h_SB001_grid_T" domain_ref="SB001" >
            <field field_ref="toce"       name="thetao_SB001"   long_name="sea water potential temperature" />
            <field field_ref="soce"       name="so_SB001"       long_name="sea water salinity"              />
            ...
            <field field_ref="vtau"       name="vtau_SB100"     long_name="surface downward y stress" />
            <field field_ref="ssh"        name="ssh_SB100"      long_name="sea surface height"/>
          </field_group>
        </file>
        <file id="file034" name_suffix="_SB034_grid_T" description="ocean T grid variables">
          <field_group id="1h_SB3301_grid_T" domain_ref="SB3301" >
            <field field_ref="toce"       name="thetao_SB3301"   long_name="sea water potential temperature" />
            <field field_ref="soce"       name="so_SB3301"       long_name="sea water salinity"              />
            ...
            <field field_ref="vtau"       name="vtau_SB3305"     long_name="surface downward y stress" />
            <field field_ref="ssh"        name="ssh_SB3305"      long_name="sea surface height"/>
          </field_group>
        </file>
      </file_group>

    cp iodef_sbmoorings_100moorings_2files.xml iodef.xml

Resubmit (Note I killed the original submission to fix the namelist_cfg sed edit issue with ``nn_write`` not being changed)::

  ./run_nemo
  4003471.sdb

**PENDING (21 Oct 2016)**
``cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings2``


* Are there 1 file of 100 moorings and 1 files of 5 moorings?
* How is the speed up with twice as many XIOS processors?

| ``OUTPUT/AMM60_1h_20120601_20120601_SB001_grid_T.nc`` exists but is not readable by Ferret or ``ncdump``
| run_counter.txt completed but new date is garbled
| wall time of 2mins was used
| ``less time.step: 1264871``
| core dump

In the LOGS/restart::

  less stdouterr

  terminate called after throwing an instance of 'xios::CNetCdfException'
    what():  Error in calling function nc_enddef(ncId)
  NetCDF: HDF error
  Unable to end define mode of this file, given its id : 65536

  forrtl: error (76): Abort trap signal
  Image              PC                Routine            Line        Source
  xios_server.exe    0000000000CB57E1  Unknown               Unknown  Unknown
  xios_server.exe    0000000000CB3F37  Unknown               Unknown  Unknown


-------

Try and Do these runs in Karen's compiled code
==============================================

Start with old directory::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago
  mkdir SBmoorings3


Copy files but not directories::

  cp EXP_NSea/* EXP_SBmoorings3/.

Link restart files::

  mkdir EXP_SBmoorings3/RESTART
  ln -s  /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/EXPD376/RESTART/01264320  EXP_SBmoorings3/RESTART/.

Edit run_counter (run for 24 hours)::

  cd EXP_SBmoorings3
  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1265760


Edit submission script, and maybe the wall time::

  vi submit_nemo.pbs
  #PBS -N AMM60_SB3
  #PBS -l walltime=00:20:00

Edit run file for new directory path::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings/run_nemo .
  vi run_nemo
  export RUNNAME=EXP_SBmoorings3
  ..
  export HOMEDIR=/work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago

Check max restarts too.
Note where field_def.xml is copied from.

Copy the other XML files::

  mkdir /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/SHARED
  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/SHARED/field_def.xml /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/SHARED/.
  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings/domain_def.xml .

Edit ``iodef.xml`` file to have 100 moorings in one file and 5 in the second (last) file::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings2/iodef_sbmoorings_100moorings_2files.xml .
  less iodef_sbmoorings_100moorings_2files.xml

  <!-- Shelf Break virtual moorings -->
      <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->
        <file id="file001" name_suffix="_SB001_grid_T" description="ocean T grid variables">
          <field_group id="1h_SB001_grid_T" domain_ref="SB001" >
            <field field_ref="toce"       name="thetao_SB001"   long_name="sea water potential temperature" />
            <field field_ref="soce"       name="so_SB001"       long_name="sea water salinity"              />
            ...
            <field field_ref="vtau"       name="vtau_SB100"     long_name="surface downward y stress" />
            <field field_ref="ssh"        name="ssh_SB100"      long_name="sea surface height"/>
          </field_group>
        </file>
        <file id="file034" name_suffix="_SB034_grid_T" description="ocean T grid variables">
          <field_group id="1h_SB3301_grid_T" domain_ref="SB3301" >
            <field field_ref="toce"       name="thetao_SB3301"   long_name="sea water potential temperature" />
            <field field_ref="soce"       name="so_SB3301"       long_name="sea water salinity"              />
            ...
            <field field_ref="vtau"       name="vtau_SB3305"     long_name="surface downward y stress" />
            <field field_ref="ssh"        name="ssh_SB3305"      long_name="sea surface height"/>
          </field_group>
        </file>
      </file_group>

      cp iodef_sbmoorings_100moorings_2files.xml iodef.xml

Resubmit (Note I killed the original submission to fix the namelist_cfg sed edit issue with ``nn_write`` not being changed)::

  ./run_nemo
  4004908.sdb

**PENDING (21 Oct 2016)**
``cd /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_SBmoorings3``




| stopped in 21s
| core dump
| empty OUTPUT/
| ``less LOGS/restart/stdouterr``: Can not open <./field_def.xml> file

| Fix missing field_def.xml (edited instructions above)
| Trim run_counter.txt

Resubmit::

  ./run_nemo
  4005687.sdb

**PENDING (22 Oct 2016)**
``cd /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_SBmoorings3``

* Are there 1 file of 100 moorings and 1 files of 5 moorings?
* How is the speed up with twice as many XIOS processors?

| killed. Wall time exceeded

Extend wall time, check ``run_counter.txt`` and resubmit::

  vi submit_nemo.pbs
  #PBS -l walltime=00:40:00

  ./run_nemo
  4006114.sdb

**PENDING (22 Oct 2016)**
``cd /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_SBmoorings3``

* Are there 1 file of 100 moorings and 1 files of 5 moorings?
* How is the speed up with twice as many XIOS processors?

| killed. Wall time exceeded

Extend wall time, check ``run_counter.txt`` and resubmit::

  vi run_counter.txt  # This is one day
  1 1 7200 20100105
  2 1264321 1265760

  vi submit_nemo.pbs
  #PBS -l walltime=01:30:00

  ./run_nemo
  4006114.sdb

**PENDING (22 Oct 2016)**
``cd /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_SBmoorings3``

* Are there 1 file of 100 moorings and 1 files of 5 moorings?
* How is the speed up with twice as many XIOS processors?

**Wall time exceeded, 1h 30.**
This is for a 1 day simulation!


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

New x10 XIOS nodes::

  submit_nemo.pbs:
  #PBS -l select=**164**
  export NEMOproc=2000
  export XIOSproc=**400**
  aprun -b -n $NEMOproc -N 24 ./$EXEC : -N 5 -n $XIOSproc ./xios_server.exe >&stdouterr

  vi run_nemo
  export NPROC=2000

  Sums:
  NEMO nodes: ceil(2000 / 24) = 84
  XIOS nodes: ceil(400 / 5) = 80
  Total = 164

Trim ``run_counter.txt``

Resubmit to a 20min queue::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_SBmoorings3
  ./run_nemo
  4007516.sdb

**FAILED (23 Oct 2016)**

* Are there 33 files of 100 moorings + 5 in another?
* How is the speed up with twice as many XIOS processors?

| It completed in 17s!
``stdouterr: apsched: claim exceeds reservation's resources``


Reduce the number of points in the moorings (from 4 to 1). And subsample space
==============================================================================

Revert back to my compiled code::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings

Manually edit domain_def.xml (1pt per mooring). LATER edit and iodef.xml (moorings divisible by 4)::

  cp domain_def.xml domain_def_4pt.xml
  mv domain_def.xml domain_def_1pt.xml

  vi domain_def_1pt.xml
  :12,3318s/zoom_ni="2" zoom_nj="2"/zoom_ni="1" zoom_nj="1"/g

  cp domain_def_1pt.xml domain_def.xml

Since 30 moorings with 4pts worked in the test phase, then 100 moorings should work with 1pt::

  cp ../EXP_SBmoorings2/iodef_sbmoorings_100moorings_2files.xml ../EXP_SBmoorings/iodef_sbmoorings_100moorings_2files.xml
  cp iodef_sbmoorings_100moorings_2files.xml iodef.xml

Edit ``run_counter.txt`` for 5 day simulation::

  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520

Extended XIOS processors (20 mins wall time)::

  vi submit_nemo.pbs
  #PBS -l select=104
  #PBS -l walltime=00:20:00
  export NEMOproc=2000
  export XIOSproc=60
  aprun -b -n $NEMOproc -N 24 ./$EXEC : -N 3 -n $XIOSproc ./xios_server.exe >&stdouterr

Resubmit::

  ./run_nemo
  4007894.sdb

cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings

**PENDING (24 Oct 2016)**

* Are there 2 files with 100 moorings in one + 5 in another?
* How is the speed?

| NOT WORKING.
| There is only one file: ``OUTPUT/AMM60_1h_20120601_20120605_SB001_grid_T.nc`` and it is broken.
| core dump
| ran for 2min 33
| less time.step: 1264875
| completed with garbled new datestamp on run_counter.txt
| less stdouterr:
::
  terminate called after throwing an instance of 'xios::CNetCdfException'
  what():  Error in calling function nc_enddef(ncId)
  NetCDF: HDF error
  Unable to end define mode of this file, given its id : 65536

  forrtl: error (76): Abort trap signal
  Image              PC                Routine            Line        Source
  xios_server.exe    0000000000CB57E1  Unknown               Unknown  Unknown
  xios_server.exe    0000000000CB3F37  Unknown               Unknown  Unknown

PERHAPS THERE IS A BUG WITH THE SECOND XML OUTPUT FILE.

**ACTION: DEBUG XML in AMM7.**

| AMM7 suggests that there are too many moorings per file.
| AMM7 configuration works with 25 moorings per file. Also 4pt per mooring increases run time by around 16%.

Try 1pt moorings. 25 moorings in 133 files.
::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings
  cp domain_def_1pt.xml domain_def.xml
  cp /work/n01/n01/jelt/gmaya/NEMO/CONFIG/XIOS_AMM7_nemo2/EXP00/iodef_sbmoorings_25moorings_133files.xml . # Get the new iodef file
  cp iodef_sbmoorings_25moorings_133files.xml iodef.xml

Trim run_counter.txt::
  vi run_counter.txt  # This is one day
  1 1 7200 20100105
  2 1264321 1265760

20min queue. Resubmit::
  ./run_nemo
  4015266.sdb

WALLTIME EXCEEDED


Resubmit with only 10 output files. Check run_counter.txt::
  ./run_nemo
  4016256.sdb

WALLTIME EXCEEDED


PLAN: OUTPUT 3D data with a SBmooring mask.
+++++++++++++++++++++++++++++++++++++++++++

NEED TO EDIT AND RECOMPILE THE CODE

Copy the modifications to the executable::

  cp /work/n01/n01/jelt/src/NEMO_V3.6_STABLE_r6232/NEMOGCM/CONFIG/XIOS_AMM7_nemo/MY_SRC/diawri.F90 /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/MY_SRC/.

Edit field_def.xml to SHAREDDIR::

  **CHANGE FIELD_REF to ID**
  vi /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/SHARED/field_def.xml

    <field_group id="sbmooring" >
     <field field_ref="sb_toce"         name="thetao"   long_name="sea_water_potential_temperature"    unit="degC"   grid_ref="grid_T_3D"  />
     <field field_ref="sb_soce"         name="so"       long_name="sea_water_salinity"                 unit="psu"   grid_ref="grid_T_3D"   />
     <field field_ref="sb_u"         name="uo"       long_name="sea_water_x_velocity"        unit="m/s"      grid_ref="grid_U_3D"          />
     <field field_ref="sb_v"         name="vo"       long_name="sea_water_y_velocity"        unit="m/s"      grid_ref="grid_V_3D"          />
     <field field_ref="sb_w"         name="wo"       long_name="sea_water_z_velocity"        unit="m/s"      grid_ref="grid_W_3D"          />
     <field field_ref="sb_dept"      name="depth"    long_name="T-cell thickness"            unit="m"        grid_ref="grid_T_3D"          />
     <field field_ref="sb_ssh"          name="zos"      long_name="sea_surface_height_above_geoid"    unit="m"     grid_ref="grid_T_2D"    />
     <field field_ref="sb_utau"         name="tauuo"   long_name="surface_downward_x_stress" unit="m/s^2"    grid_ref="grid_U_2D" />
     <field field_ref="sb_vtau"         name="tauvo"   long_name="surface_downward_y_stress" unit="m/s^2"    grid_ref="grid_V_2D" />
    </field_group>



Edit iodef.xml in JOBDIR::
    vi iodef.xml
      <!-- Shelf Break virtual moorings -->
      <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->
        <file id="file51" name_suffix="SB" description="Shelf break moorings">
          <field_group group_ref="sbmoorings"/>
        </file>
        ..
      </file_group>


Recompile

load modules::

  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel

Compile::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG
  ./makenemo -n XIOS_AMM60_nemo_harmIT -m XC_ARCHER_INTEL -j 10

Copy executable (note that this is for 3D harmonics, though it is not invoked here)::
  mv XIOS_AMM60_nemo/EXP_SBmoorings/nemo.exe  XIOS_AMM60_nemo/EXP_SBmoorings/nemo.exe301016
  cp  XIOS_AMM60_nemo_harmIT/BLD/bin/nemo.exe XIOS_AMM60_nemo/EXP_SBmoorings/.


Trim run_counter.txt::

  cd XIOS_AMM60_nemo/EXP_SBmoorings/
  vi run_counter.txt

**Hit wall limit** Need to do some more debugging in AMM7

Resubmit::

  ./run_nemo
  4021532.sdb

Hit wall time. No output. XML only sought 3d sbmooring which is enabled in .F90

Resubmit with one simple output to check F90 (2 moorings in one file) and no 3d sbmoorings::

  ./run_nemo
  4021929.sdb

STILL HITTING WALL TIME. SOMETHING WRONG WITH 3D harmonics not CORRECT (nit000_han and nitend_han). UPDATE run_nemo (using ../../XIOS_AMM60_nemo_harmIT/EXP_harmIT/run_nemo) and RESUBMIT::

  ./run_nemo
  4022079.sdb


**This is running and outputting 1d mooring files**
Run to competion (5mins for 1 day) but also creates a core dump. Data in 1d mooring file is not OK. Time axis runs L=1:0. THIS MAY WELL BE RESOLVED WITH A LONGER RUN.

NEXT STEP TO TRY AGAIN WITH 3D moorings
Trim run_counter.txt
Edit iodef.xml to include 3d sbmoorings::

  <file id="file51" name_suffix="SB" description="Shelf break moorings">
    <field_group group_ref="sbmooring"/>
  </file>

Iron out bug with 3D harmonics expecting extra harmonic outputs that don't exist. Edit namelist_cfg. TURN OFF 3D harmonics::

  !-----------------------------------------------------------------------
  &nam_diaharm   !   Harmonic analysis of tidal constituents ('key_diaharm')
  !-----------------------------------------------------------------------
    nit000_han = 1         ! First time step used for harmonic analysis
    nitend_han = 75        ! Last time step used for harmonic analysis
    nstep_han  = 15        ! Time step frequency for harmonic analysis
    tname(1)     =   'O1'  !  name of constituent
    tname(2)     =   'P1'
    tname(3)     =   'K1'
    tname(4)     =   'N2'
    tname(5)     =   'M2'
    tname(6)     =   'S2'
    tname(7)     =   'K2'
    tname(8)     =   'Q1'
    tname(9)     =   'M4'
  /
  !-----------------------------------------------------------------------
  &nam_dia25h    !   Output 25 hour mean diagnostics
  !-----------------------------------------------------------------------
   ln_dia25h   = .false.

Resubmit::

  ./run_nemo
  4022202.sdb

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings

**WORKED AND COMPLETED IN 5mins for 1 day**
1d mooring data was outputted (though data was missing for hours 1 : 10, present from 11 : 24 ?)

----

This was from an executable copied from ``XIOS_AMM60_nemo_harmIT``. Compile fresh and rerun. It makes little sense to be developing to code bases,
with and without 3d harmonics when namelist switches can be used. Make a backup copy of MY_SRC and then copy from  XIOS_AMM60_nemo_harmIT::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo
  cp -r MY_SRC MY_SRC_1Nov16

  cp -r ../XIOS_AMM60_nemo_harmIT/MY_SRC MY_SRC

Note there was an oddity in ``diawri.F90`` with

 ``CALL iom_put( "rhop_surf", rhop(:,:,1) )   ! surface potential density.``
  But ``rhop_surf`` does not exist. Comment out this line.


...
::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG

  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel

Add key_diaharm flag::

  vi XIOS_AMM60_nemo/cpp_XIOS_AMM60_nemo.fcm
  bld::tool::fppkeys     key_ldfslp key_iomput key_mpp_mpi key_netcdf4 key_tide key_bdy key_jdha_init key_dynspg_ts key_vvl key_zdfgls key_dynldf_smag key_traldf_smag key_traldf_c3d    key_dynldf_c3d   key_diaharm

Compile::

  ./makenemo -n XIOS_AMM60_nemo -m XC_ARCHER_INTEL -j 10 clean
  ./makenemo -n XIOS_AMM60_nemo -m XC_ARCHER_INTEL -j 10

Copy executable (or check that the symbolic link is correct)::
  cp  XIOS_AMM60_nemo/BLD/bin/nemo.exe XIOS_AMM60_nemo/EXP_SBmoorings/.


Trim run_counter.txt::

  cd XIOS_AMM60_nemo/EXP_SBmoorings/
  vi run_counter.txt

Resubmit::

  ./run_nemo
  4022946.sdb


With new sbmask and karen's code. This works.
Note the the sbmask is only defined on each process at the moment.

Size of 3x3 output, on each processor, which is 5236 moorings, hourly output for one day is 45G.
This would need offline compression.

Edit compression script, then submit::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings
  vi nc_compress.pbs
  qsub nc_compress.pbs

This hit the Wall time limit (60mins) and so did not work. Extend wall time to 2 hours::

  qsub nc_compress.pbs

| **This took 2hr 6mins to process one 45Gb file (1 day of data), which took 6 mins to generate..**
| **Compressed size 294Mb**

Investigate better compression using on-line code. Have a trawl of James' filespace for examples of ``xmlio_server.def``.
Copy and update the nn_chunks_k. Note AMM60 has 51 levels, NNA has 75::

  cp /work/n01/n01/jdha/ST/trunk/NEMOGCM/CONFIG/AMM7/EXP00/xmlio_server.def /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings/xmlio_server.def
  vi xmlio_server.def
  ...
  !-----------------------------------------------------------------------
  &namnc4         !  netcdf4 chunking and compression settings
                  !  (benign if "key_netcdf4" is not used)
  !-----------------------------------------------------------------------
     nn_nchunks_i   =   4       !  number of chunks in i-dimension
     nn_nchunks_j   =   4       !  number of chunks in j-dimension
     nn_nchunks_k   =   6      !  number of chunks in k-dimension
                                !  setting nn_nchunks_k = jpk will give a chunk size of 1 in the vertical which
                                !  is optimal for postprocessing which works exclusively with horizontal slabs
     ln_nc4zip      =   .TRUE.  !  (T) use netcdf4 chunking and compression
                                !  (F) ignore chunking information and produce netcdf3-compatible files


Trim run_counter.txt::

  cd XIOS_AMM60_nemo/EXP_SBmoorings/
  vi run_counter.txt

Resubmit::

    ./run_nemo
    4024719.sdb

** Completed in 5min 57**
AMM60_1h_20120601_20120601_SB.nc is still 45Gb, though AMM60_1h_20120601_20120601_SB001_grid_T.nc is probably smaller.

----

========================================
Outputting moorings as a masked 3D array
========================================

Source: ``/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings``

* Need to create a globally accessible mask file ``sbmask``
* Need to write output

Create a mask file
==================

Make some edits to move sbmask definition to dom_oce.F90::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/NEMO/OPA_SRC

  cp DOM/dommsk.F90 ../../CONFIG/XIOS_AMM60_nemo/MY_SRC/dommsk.F90
  cp DOM/dom_oce.F90 ../../CONFIG/XIOS_AMM60_nemo/MY_SRC/dom_oce.F90

Edit sbmask files::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/MY_SRC
  vi dommsk.F90
  ...

  vi dom_oce.F90
  ...

Compile::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG

  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel

  ./makenemo -n XIOS_AMM60_nemo -m XC_ARCHER_INTEL -j 10


Trim run_counter.txt::

  cd XIOS_AMM60_nemo/EXP_SBmoorings/
  vi run_counter.txt

Resubmit::

    ./run_nemo
    4023900.sdb

**PENDING (1 Nov 2016)**
cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings


* Does the output work? **YES**
* How fast / slow is it? **6mins / day**
* How large is the output? **45Gb / day**
* Implement proper IF statements in diawri.F90


Edit domain_def.xml to output region of interest only::
  vi domain_def.xml

  <!-- Shelf Break virtual mooring mask -->
  <domain id="SB" zoom_ibegin="0307" zoom_jbegin="0252" zoom_ni="0666" zoom_nj="0986" />


Add subdomain ``SB`` to iodef.xml file::

  vi iodef.xml
  ...
      <!-- Shelf Break virtual moorings -->
      <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->

        <file id="file51" name_suffix="_SB" description="Shelf break moorings">
          <field_group group_ref="sbmooring" domain_ref="SB"/>
        </file>

      </file_group>

Run for 1 day. Trim run_counter.txt::

    cd XIOS_AMM60_nemo/EXP_SBmoorings/
    vi run_counter.txt




Simulation plan for June 2012
=============================

Presently from run_counter.txt
6 minutes a day. Therefore 3 day chunks will fit on the 20mins queue

1264321 1265760 - 1 day

1264321 1268640 - 3 days

For June 2012 need 10 loops of 3 days::

  vi run_counter.txt
  1 1 7200 20100105
  1264321 1268640

  vi run_nemo
  export nrestart_max=11 #31 (For one submission this number must equal the number of lines in run_counter.txt)

I made these edits after the job was submitted but was still in the queue. Perhaps it will work...

Resubmit::

    ./run_nemo
    4024958.sdb


Editted finish_nemo.sh to launch compression of data::

  vi finish_nemo.sh
  ...
  # Compress output
  qsub $JOBDIR/nc_compress.pbs

The compression script moves compressed files to OUTPUT/tmp. This needs to be cleaned when the compressed files are checked.
** This didn't seem to work properly:**, though output seemed to compress fine the log files were lost and the job ran until walltime limit**


**PENDING (2 Nov 2016)**
cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings

* Does the output work? **YES**
* How fast / slow is it? **> 6mins / day ?** Between 5mins 14 and 7mins 48
* How large is the output? **< 45Gb / day** 18Gb uncompressed, 322Mb compressed
* Clean the OUTPUT/tmp directory if output is OK.
* nc_compress.pbs: 1hr 35

**ACTION: copy to SAN**::
  rsync -uartv jelt@login.archer.ac.uk:/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings/OUTPUT/cmp* /projectsa/FASTNEt/jelt/AMM60/RUNS/2010_2013/SB_moorings/.
  chmod a+rx /projectsa/FASTNEt/jelt/AMM60/RUNS/2010_2013/SB_moorings/cmp*nc


Job has finished but the 1 June file is missing
Edit run_counter.txt to just do one day::

  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1265760

Here is the completed ``run_counter.txt`` file with 1 day increments.
1 1 7200 20100105
2 1264321 1265760
3 1265761 1267200 1265760=20120601
4 1267201 1268640 1267200=20120602
5 1268641 1270080 1268640=20120603
6 1270081 1271520 1270080=20120604
7 1271521 1272960 1271520=20120605
8 1272961 1274400 1272960=20120606
9 1274401 1275840 1274400=20120607
10 1275841 1277280 1275840=20120608
11 1277281 1278720 1277280=20120609
12 1278721 1280160 1278720=20120610
13 1280161 1281600 1280160=20120611
14 1281601 1283040 1281600=20120612
15 1283041 1284480 1283040=20120613
16 1284481 1285920 1284480=20120614
17 1285921 1287360 1285920=20120615
18 1287361 1288800 1287360=20120616
19 1288801 1290240 1288800=20120617
20 1290241 1291680 1290240=20120618
21 1291681 1293120 1291680=20120619
22 1293121 1294560 1293120=20120620
23 1294561 1296000 1294560=20120621
24 1296001 1297440 1296000=20120622
25 1297441 1298880 1297440=20120623
26 1298881 1300320 1298880=20120624
27 1300321 1301760 1300320=20120625
28 1301761 1303200 1301760=20120626
29 1303201 1304640 1303200=20120627
30 1304641 1306080 1304640=20120628
31 1306081 1307520 1306080=20120629
32 1307521 1308960 1307520=20120630


**ACTIONS:**
* Check qstat progress on 1st June. **DONE**.
* Check progress of nc_compress. How long did it take? **1hr 35** **DONE**.
* Submit lots of nc_compress jobs with revised wall time estimates. **DONE**
* Submit last nc_compress1_9_10.pbs when only 3 files left in OUTPUT. **DONE**
* LATER: scp cmp\*nc files to SAN. **DONE**
* gzip OUTPUT/cmp\*nc files **DONE**
* delete EXP_SBmoorings2 EXP_SBmoorings3
* move EXP_SBmoorings to /nerc

*(31 Jan 2016)*
Over wrote run_nemo and submit_nemo.pbs by mistake. Replaced them with old
copies. I think that they are OK but haven't tested.
Noted that extra processes are used for the XIOS steps (XIOSproc=60 instead of 40 running on 92 nodes). These are probably not
needed with the 3D masked array output method.
