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
