======================
Debugging XIOS in AMM7
======================


Debugging XML errors in AMM60 is time consuming because of the queue duration. Here debug with AMM7 and submissions to the short queue.
NB: I had trouble getting a new clean run working. No docs! So I've just started woring in the following:

Source: ``cd /work/n01/n01/jelt/gmaya/NEMO/CONFIG/XIOS_AMM7_nemo2/EXP00``

Test it works::

 ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
 3990406.sdb

**This works!**

Write multiple copies of same variable in single file
=====================================================

Make a safe copy of ``iodef.xml`` then edit a working copy with two different tos1 and tos2 variables::

 mv iodef.xml iodef.xml_141016
 cp ../../XIOS_AMM7_nemo/EXP00/iodef.xml iodef.xml


Edit the working XML file

     <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE." > <!-- 1h files -->
        <file id="file51" name_suffix="_grid_T" description="ocean T grid variables" >
         <field_group id="test1">
          <field field_ref="sst"          name="tos1" />
         </field_group>
         <field_group id="test2">
          <field field_ref="sst"          name="tos2" />
         </field_group>
        </file>
      </file_group>

Resubmit::

 ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
 3991577.sdb



This WORKS **(18 Oct 2016)**. Mount ARCHER with ``sshfs`` and visualise in FERRET::

 jeff% cd /Volumes/archer/gmaya/NEMO/CONFIG/XIOS_AMM7_nemo2/EXP00
 [livmaf:CONFIG/XIOS_AMM7_nemo2/EXP00] jeff% ferret
 yes? use amm7_1h_19810101_19810110_grid_T.nc
 yes? shade/L=240 TOS2-TOS1
 yes? shade/L=240 TOS1
 yes? shade/L=240 TOS2


Write two copies of same variable in different domains in single file
=====================================================================

Edit the ``domain_def.xml`` file (these points are Malin, SB001, and North Sea, SB002)::

  <domain id="SB001" zoom_ibegin="0100" zoom_jbegin="0250" zoom_ni="2" zoom_nj="2" />
  <domain id="SB002" zoom_ibegin="0200" zoom_jbegin="0250" zoom_ni="2" zoom_nj="2" />

Edit the ``iodef.xml`` file::

     <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE." > <!-- 1h files -->
        <file id="file51" name_suffix="_grid_T" description="ocean T grid variables" >
         <field_group id="test1" domain_ref="SB001">
          <field field_ref="sst"          name="tos1" />
         </field_group>
         <field_group id="test2" domain_ref="SB002">
          <field field_ref="sst"          name="tos2" />
         </field_group>
        </file>
      </file_group>

Resubmit::

 ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
 3996333.sdb

THIS WORKS! and outputs LAT and LON variables for each mooring::

   yes? sh da
     currently SET data sets:
    1> ./amm7_1h_19810101_19810110_grid_T.nc  (default)
   name     title                             I         J         K         L         M         N
   NAV_LAT_SB001
          Latitude                         1:2       1:2       ...       ...       ...       ...
   NAV_LON_SB001
          Longitude                        1:2       1:2       ...       ...       ...       ...
   NAV_LAT_SB002
          Latitude                         1:2       1:2       ...       ...       ...       ...
   NAV_LON_SB002
          Longitude                        1:2       1:2       ...       ...       ...       ...
   TOS1     sea surface temperature        1:2       1:2       ...       1:240     ...       ...
   TIME_CENTERED
          Time axis                        ...       ...       ...       1:240     ...       ...
   TIME_CENTERED_BOUNDS
                                           1:2       ...       ...       1:240     ...       ...
   TOS2     sea surface temperature        1:2       1:2       ...       1:240     ...       ...


----

Edit: if ``iodef.xml`` has long_name the same for both variables does it work?::

  vi iodef.xml
  ...
  <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE." > <!-- 1h files -->
   <file id="file51" name_suffix="_grid_T" description="ocean T grid variables" >
    <field_group id="test1" domain_ref="SB001">
     <field field_ref="sst"          name="tos1" long_name="sea surface temperature" />
    </field_group>
    <field_group id="test2" domain_ref="SB002">
     <field field_ref="sst"          name="tos2" long_name="sea surface temperature" />
    </field_group>
   </file>
  </file_group>

Resubmit::

 ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
 3996492.sdb

YES. This also works.

----

Can I break it if I copy code from the failing AMM60 ``iodef.xml`` file?::

  <!-- Shelf Break virtual moorings -->
        <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->"
          <file id="file0000" name_suffix="_SB_grid_T" description="ocean T grid variables">
            <field_group id="1h_SB001_grid_T" domain_ref="SB001" >
              <field field_ref="toce"       name="thetao_SB001"   long_name="sea water potential temperature" />
              <field field_ref="soce"       name="so_SB001"       long_name="sea water salinity"              />
              <field field_ref="tpt_dep"    name="depth_SB001"   />
              <field field_ref="uoce"       name="vozocrtx_SB001" long_name="sea water x velocity" />
              <field field_ref="voce"       name="vomecrty_SB001" long_name="sea water y velocity" />
              <field field_ref="woce"       name="vovecrtz_SB001" long_name="sea water w velocity" />
              <field field_ref="utau"       name="utau_SB001"     long_name="surface downward x stress" />
              <field field_ref="vtau"       name="vtau_SB001"     long_name="surface downward y stress" />
              <field field_ref="ssh"        name="ssh_SB001"      long_name="sea surface height"/>
            </field_group>
            <field_group id="1h_SB002_grid_T" domain_ref="SB002" >
              <field field_ref="toce"       name="thetao_SB002"   long_name="sea water potential temperature" />
              <field field_ref="soce"       name="so_SB002"       long_name="sea water salinity"              />
              <field field_ref="tpt_dep"    name="depth_SB002"   />
              <field field_ref="uoce"       name="vozocrtx_SB002" long_name="sea water x velocity" />
              <field field_ref="voce"       name="vomecrty_SB002" long_name="sea water y velocity" />
              <field field_ref="woce"       name="vovecrtz_SB002" long_name="sea water w velocity" />
              <field field_ref="utau"       name="utau_SB002"     long_name="surface downward x stress" />
              <field field_ref="vtau"       name="vtau_SB002"     long_name="surface downward y stress" />
              <field field_ref="ssh"        name="ssh_SB002"      long_name="sea surface height"/>
            </field_group>
        	</file>
        </file_group>

Note that these domain_ref locations are set in domain_ref.xml and are OK for AMM7

Resubmit::

 ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
 3996586.sdb


Ah Ha! This runs but does not produce netCDF output. Good (because it can be fixed!)

Change filename::

  <!-- Shelf Break virtual moorings -->
      <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->"
        <file id="file51" name_suffix="_SB_grid_T" description="ocean T grid variables">
          <field_group id="1h_SB001_grid_T" domain_ref="SB001" >

Nope

Switch fields (``iodef.xml_fields_notworking``)::

  <!-- Shelf Break virtual moorings -->
        <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->"
          <file id="file0000" name_suffix="_SB_grid_T" description="ocean T grid variables">
            <field_group id="1h_SB001_grid_T" domain_ref="SB001" >
              <field field_ref="sst"          name="tos1" long_name="sea surface temperature" />
            </field_group>
            <field_group id="1h_SB002_grid_T" domain_ref="SB002" >
             <field field_ref="sst"          name="tos2" long_name="sea surface temperature" />
            </field_group>
        	</file>
        </file_group>

Nope

Try something that did work (``iodef.xml_field_working``)::


  <!-- Shelf Break virtual moorings -->
    <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE." > <!-- 1h files -->
     <file id="file51" name_suffix="_grid_T" description="ocean T grid variables" >
      <field_group id="test1" domain_ref="SB001">
       <field field_ref="sst"          name="tos1" long_name="sea surface temperature" />
      </field_group>
      <field_group id="test2" domain_ref="SB002">
       <field field_ref="sst"          name="tos2" long_name="sea surface temperature" />
      </field_group>
     </file>
    </file_group>

Now try and change stuff until it breaks (``iodef.xml_file_id_``)::

  <!-- Shelf Break virtual moorings -->
     <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->"
       <file id="file51" name_suffix="_grid_T" description="ocean T grid variables" >
          <field_group id="1h_SB001_grid_T" domain_ref="SB001" >
            <field field_ref="sst"          name="tos1" long_name="sea surface temperature" />
          </field_group>
          <field_group id="1h_SB002_grid_T" domain_ref="SB002" >
            <field field_ref="sst"          name="tos2" long_name="sea surface temperature" />
          </field_group>
       </file>
     </file_group>


Spotted a spurious **"** in the ``file_group`` definition line.
Remove and try the tricky thing again::

    <!-- Shelf Break virtual moorings -->
          <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->
            <file id="file0000" name_suffix="_SB_grid_T" description="ocean T grid variables">
              <field_group id="1h_SB001_grid_T" domain_ref="SB001" >
                <field field_ref="toce"       name="thetao_SB001"   long_name="sea water potential temperature" />
                <field field_ref="soce"       name="so_SB001"       long_name="sea water salinity"              />
                <field field_ref="tpt_dep"    name="depth_SB001"   />
                <field field_ref="uoce"       name="vozocrtx_SB001" long_name="sea water x velocity" />
                <field field_ref="voce"       name="vomecrty_SB001" long_name="sea water y velocity" />
                <field field_ref="woce"       name="vovecrtz_SB001" long_name="sea water w velocity" />
                <field field_ref="utau"       name="utau_SB001"     long_name="surface downward x stress" />
                <field field_ref="vtau"       name="vtau_SB001"     long_name="surface downward y stress" />
                <field field_ref="ssh"        name="ssh_SB001"      long_name="sea surface height"/>
              </field_group>
              <field_group id="1h_SB002_grid_T" domain_ref="SB002" >
                <field field_ref="toce"       name="thetao_SB002"   long_name="sea water potential temperature" />
                <field field_ref="soce"       name="so_SB002"       long_name="sea water salinity"              />
                <field field_ref="tpt_dep"    name="depth_SB002"   />
                <field field_ref="uoce"       name="vozocrtx_SB002" long_name="sea water x velocity" />
                <field field_ref="voce"       name="vomecrty_SB002" long_name="sea water y velocity" />
                <field field_ref="woce"       name="vovecrtz_SB002" long_name="sea water w velocity" />
                <field field_ref="utau"       name="utau_SB002"     long_name="surface downward x stress" />
                <field field_ref="vtau"       name="vtau_SB002"     long_name="surface downward y stress" />
                <field field_ref="ssh"        name="ssh_SB002"      long_name="sea surface height"/>
              </field_group>
            </file>
          </file_group>

This creates a netCDF output file but the file name is messed up because ``file id="file0000"`` is out of range (1-999).

Change filename file id="file51" and resubmit. THIS WORKS.

----------

AMM7 with two moorings files
============================

cd /work/n01/n01/jelt/gmaya/NEMO/CONFIG/XIOS_AMM7_nemo2/EXP00

Edit ``iodef.xml`` to output two files with two moorings::

  vi iodef.xml
  <!-- Shelf Break virtual moorings -->
        <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->
          <file id="file001" name_suffix="_SB001_grid_T" description="ocean T grid variables">
            <field_group id="1h_SB001_grid_T" domain_ref="SB001" >
              <field field_ref="toce"       name="thetao_SB001"   long_name="sea water potential temperature" />
              <field field_ref="soce"       name="so_SB001"       long_name="sea water salinity"              />
              <field field_ref="tpt_dep"    name="depth_SB001"   />
              <field field_ref="uoce"       name="vozocrtx_SB001" long_name="sea water x velocity" />
              <field field_ref="voce"       name="vomecrty_SB001" long_name="sea water y velocity" />
              <field field_ref="woce"       name="vovecrtz_SB001" long_name="sea water w velocity" />
              <field field_ref="utau"       name="utau_SB001"     long_name="surface downward x stress" />
              <field field_ref="vtau"       name="vtau_SB001"     long_name="surface downward y stress" />
              <field field_ref="ssh"        name="ssh_SB001"      long_name="sea surface height"/>
            </field_group>
            <field_group id="1h_SB002_grid_T" domain_ref="SB002" >
              <field field_ref="toce"       name="thetao_SB002"   long_name="sea water potential temperature" />
              <field field_ref="soce"       name="so_SB002"       long_name="sea water salinity"              />
              <field field_ref="tpt_dep"    name="depth_SB002"   />
              <field field_ref="uoce"       name="vozocrtx_SB002" long_name="sea water x velocity" />
              <field field_ref="voce"       name="vomecrty_SB002" long_name="sea water y velocity" />
              <field field_ref="woce"       name="vovecrtz_SB002" long_name="sea water w velocity" />
              <field field_ref="utau"       name="utau_SB002"     long_name="surface downward x stress" />
              <field field_ref="vtau"       name="vtau_SB002"     long_name="surface downward y stress" />
              <field field_ref="ssh"        name="ssh_SB002"      long_name="sea surface height"/>
            </field_group>
          </file>
          <file id="file002" name_suffix="_SB002_grid_T" description="ocean T grid variables">
            <field_group id="1h_SB003_grid_T" domain_ref="SB003" >
              <field field_ref="toce"       name="thetao_SB003"   long_name="sea water potential temperature" />
              <field field_ref="soce"       name="so_SB003"       long_name="sea water salinity"              />
              <field field_ref="tpt_dep"    name="depth_SB003"   />
              <field field_ref="uoce"       name="vozocrtx_SB003" long_name="sea water x velocity" />
              <field field_ref="voce"       name="vomecrty_SB003" long_name="sea water y velocity" />
              <field field_ref="woce"       name="vovecrtz_SB003" long_name="sea water w velocity" />
              <field field_ref="utau"       name="utau_SB003"     long_name="surface downward x stress" />
              <field field_ref="vtau"       name="vtau_SB003"     long_name="surface downward y stress" />
              <field field_ref="ssh"        name="ssh_SB003"      long_name="sea surface height"/>
            </field_group>
          </file>
        </file_group>


Edit ``domain_def.xml`` to include 3rd mooring (not sure where this is)::
  vi domain_def.xml
  <!-- Test zoom for AMM7. Malin and North Sea -->
  <domain id="SB001" zoom_ibegin="0100" zoom_jbegin="0250" zoom_ni="2" zoom_nj="2" />
  <domain id="SB002" zoom_ibegin="0200" zoom_jbegin="0250" zoom_ni="2" zoom_nj="2" />
  <domain id="SB003" zoom_ibegin="0050" zoom_jbegin="0050" zoom_ni="2" zoom_nj="2" />

Resubmit::

   ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
   4012237.sdb


**THIS WORKS**
| walltime=00:06:14


Check this works with 1pt moorings. Edit domain_def.xml

Resubmit::

   ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
   4012284.sdb

**THIS WORKS, AS EXPECTED**

cd /work/n01/n01/jelt/gmaya/NEMO/CONFIG/XIOS_AMM7_nemo2/EXP00

----

Next steps:
===========

Create mirrored AMM60 output with 100 moorings in one file and about 5 in another.

Create ``domain_def_1pt.xml`` with ipython notebook

Copy the 2 file iodef.xml file::

  cd /work/n01/n01/jelt/gmaya/NEMO/CONFIG/XIOS_AMM7_nemo2/EXP00
  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings/iodef_sbmoorings_100moorings_2files.xml .
  cp iodef_sbmoorings_100moorings_2files.xml iodef.xml

Resubmit::

   ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
   4012898.sdb

**PENDING 27 Oct 2016. Expect one file with 100 moorings and one file with 5. These are 1pt moorings**

| ran for 59s
| get NetCDF output but there is a problem with it

::
  less NE198101.e4012898

  terminate called after throwing an instance of 'terminate called after throwing an instance of 'xios::CNetCdfExceptionxios::CNetCdfException'
  '
    what():    what():  Error in calling function nc_enddef(ncId)
  NetCDF: HDF error
  Unable to end define mode of this file, given its id : 65536

Try a quick edit of ``iodef.xml``. Change the file name to see if it then works. Use filenames file001 and file002, which previously worked.
``<file id="file002" name_suffix="_SB034_grid_T" description="ocean T grid variables">``

Resubmit::

   ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
   4013865.sdb

**PENDING 28 Oct 2016. Expect one file with 100 moorings and one file with 5. These are 1pt moorings**

| ran for 1min 1s. Same error with same file id: 65536

Comment out second file in iodef.xml

Resubmit::

   ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
   4013884.sdb

| Same error!

However it worked with 2 files and 3 moorings and a long time ago with one file and 30 (4pt) moorings.

Try one file with 33 moorings. 2nd file with 5 moorings.

Resubmit::

   ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1

Same but shorter error log::
  terminate called after throwing an instance of 'xios::CNetCdfException'
  what():  Error in calling function nc_enddef(ncId)
  NetCDF: HDF error
  Unable to end define mode of this file, given its id : 65536


Remove the second file. Leaving one file with 33 (1pt) moorings. Resubmit::

  ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
  4013938.sdb

Same error,

Resubmit with 20 (1pt) moorings::

  ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
  4013970.sdb

This **WORKS** and runs beyond 2 mins.

Quit and resubmit with 25 (1pt) moorings::

  ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
  4014010.sdb

| **WORKS**
| 6 min 41s to finish. Load balance looks OK

Resubmit with 30 (1pt) moorings::

  ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
  4014047.sdb

**Fails** with new error after 2mins::

  > Error [CNc4DataOutput::writeFieldData_ (CField*  field)] : In file '/work/n01/n01/jelt/xios-1.0_r703/src/output/nc4_data_output.cpp', line 1271 -> On writin
  g field data: so_SB025
  In the context : nemo
  Error in calling function ncPutVaraType(ncid, varId, start, count, op)
  NetCDF: HDF error
  Unable to write value of a variable with id : 289


CONCLUSION. 25 moorings work but 30 moorings do not.
Try 20 moorings in one file and 5 in a second.

Resubmit with 25 (1pt) + 5 moorings::

  ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
  4014081.sdb

**WORKED** 6min 50s.
CONCLUSION. 25 moorings work in one file but 30 moorings do not.
However 25 mooorings in one file and 5 in another file also works.


Resubmit with 2 files each with 25 (1pt) moorings::

  ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
  4014137.sdb


**WORKED** walltime=00:07:48
CONCLUSION. 25 moorings each in 2 files works.
But 30 moorings in 1 file does not.

**TEST**
Does 4pt mooring effect performance?

Resubmit with 2 files each with 25 (1pt) moorings::

  cp domain_def_4pt.xml domain_def.xml
  ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
  4014418.sdb

**It worked** walltime=00:09:00
CONCLUSION. 25 (1pt) moorings each in 2 files works.
But 30 moorings in 1 file does not.
Increasing from 1pt to 4pt moorings increases walltime from 7:45 to 9:00 mins


**Test**
Use 1pt moorings for all the moorings spread across 133 files in groups of 25. Resubmit::

  cp domain_def_1pt.xml domain_def.xml
  cp iodef_sbmoorings_25moorings_133files.xml iodef.xml
  ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
  4015212.sdb

*Side line*
This looks like it is working, since it has not instantly crashed. Try 25moorings in 133 files with AMM60

EXCEEDED WALLTIME.

Trim simulation from 10 days to 1 day, keep the 20min queue::

  vi annualrun.pbs
  #nit=$((10*tpd)) # 10 days
  nit=$((1*tpd)) # 1 days

Resubmit::
  ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
  4015274.sdb

Walltime exceeded. Cut down from 133 files to 13 files in iodef.xml
Resubmit::
  ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
  4015306.sdb

Walltime exceeded.

cd /work/n01/n01/jelt/gmaya/NEMO/CONFIG/XIOS_AMM7_nemo2/EXP00


----

New Plan: Create a mask and output moorings as a 3D array
=========================================================

First check that AMM7 can compile::

  https://www.evernote.com/shard/s523/nl/2147483647/086ee834-ae54-4384-8523-79a1eee0d54e/

Submit::

  cd /work/n01/n01/jelt/src/NEMO_V3.6_STABLE_r6232/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
  4017892.sdb

Plan is to emulate the changes Karen made to diagnose Internal Tides. These edits are all confined to ``diawri.F90``
First make a copy of working ``diawri.F90``

``/work/n01/n01/jelt/src/NEMO_V3.6_STABLE_r6232/NEMOGCM/CONFIG/XIOS_AMM7_nemo/MY_SRC> cp diawri.F90  diawri.F90_30Oct16``

Edit ``diawri.F90`` (copy and paste in here when it works)::

    ...
    sbmask(161, 78) = 1
    sbmask(161, 79) = 1
    sbmask(160, 79) = 1
    sbmask(159, 79) = 1
    sbmask(158, 80) = 1

    z3d(:,:,jpk) = 0.e0
    DO jk = 1, jpkm1
       z3d(:,:,jk) = sbmask * un(:,:,jk)
    END DO
    CALL iom_put( "sb_u", z3d )   ! 3D u-velocity
    z3d(:,:,jpk) = 0.e0
    DO jk = 1, jpkm1
       z3d(:,:,jk) = sbmask * vn(:,:,jk)
    END DO
    CALL iom_put( "sb_v", z3d )   ! 3D v-velocity
    z3d(:,:,jpk) = 0.e0
    DO jk = 1, jpkm1
       z3d(:,:,jk) = sbmask * wn(:,:,jk)
    END DO
    CALL iom_put( "sb_w", z3d )   ! 3D w-velocity

    z3d(:,:,jpk) = 0.e0
    DO jk = 1, jpkm1
       z3d(:,:,jk) = sbmask * tsn(:,:,jk,jp_tem)
    END DO
    CALL iom_put( "sb_toce", z3d )   ! 3D temperature
    z3d(:,:,jpk) = 0.e0
    DO jk = 1, jpkm1
       z3d(:,:,jk) = sbmask * tsn(:,:,jk,jp_sal)
    END DO
    CALL iom_put( "sb_soce", z3d )   ! 3D salinity
    z3d(:,:,jpk) = 0.e0
    DO jk = 1, jpkm1
       z3d(:,:,jk) = sbmask * fse3t(:,:,jk)
    END DO
    CALL iom_put( "sb_dept", z3d )   ! 3D T-point depth

    CALL iom_put( "sb_utau", utau * sbmask )   ! 2D zontal wind stress
    CALL iom_put( "sb_vtau", vtau * sbmask )   ! 2D meridional wind stress
    CALL iom_put( "sb_ssh",  sshn * sbmask )   ! 2D ssh
  ENDIF



  cd /work/n01/n01/jelt/src/NEMO_V3.6_STABLE_r6232/NEMOGCM/CONFIG
  ./makenemo -n XIOS_AMM7_nemo -m ARCHER_INTEL


Copy executable:
  cp  XIOS_AMM7_nemo/BLD/bin/nemo.exe XIOS_AMM7_nemo/EXP00/.

Copy pbs script:
#  cp  /work/n01/n01/jelt/gmaya/NEMO/CONFIG/XIOS_AMM7_nemo/EXP00/annualrun.pbs ../XIOS_AMM7_nemo/EXP00/.

Edit paths in annualrun.pbs and run_nemo.sh for the appropriate execution directory::

  vi annualrun.pbs  # running for one day
  vi run_nemo.sh


Edit ``field_def.xml``::

  vi field_def.xml

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


Edit ``iodef.xml``::

  vi iodef.xml
  <file_group id="1h" output_freq="1h"  output_level="10" enabled=".TRUE."> <!-- 1h files -->
   <file id="file51" name_suffix="SB" description="Shelf break moorings">
    <field_group group_ref="sbmoorings"/>
   </file>
   ...
  </file_group>

Edit the wall time to since it appears the 24-48hr queue might be quicker (didn't work. Not sure what queue is called)::

  vi annualrun.pbs
  #PBS -l walltime=24:20:00
  #PBS -q short



Submit::
  cd /work/n01/n01/jelt/src/NEMO_V3.6_STABLE_r6232/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00
  ./run_nemo.sh annualrun.pbs 12 16 192 1981 1 1
  4017983.sdb

**PENDING (30 Oct 2016)**
cd /work/n01/n01/jelt/src/NEMO_V3.6_STABLE_r6232/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00

* Does the output work?
* How fast / slow is it?
* How large is the output?

Assume all the above is correct. Copy to AMM60, compile and submit.
