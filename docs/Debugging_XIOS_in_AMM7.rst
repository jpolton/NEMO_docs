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

**DOES IT WORK? PENDING 27 Oct 2016**

----

Next steps:
===========

Create mirrored AMM60 output with 100 moorings in one file and about 5 in another.
