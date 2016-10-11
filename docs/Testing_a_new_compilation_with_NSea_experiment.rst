==============================================
Testing a new compilation with NSea experiment
==============================================


Having first followed `Compiling AMM60 <Compiling_AMM60.html>`_ to make a new executable
Construct a EXP_NSea experiment to test new build.

Update directory with missing files to get it working
cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo
rsync -uartv /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_NSea_prelauch_copy/ ../XIOS_AMM60_nemo

mkdir EXP_NSea
Edit run_counter to start from restart on the 14th Jan 2010.
vi EXP_NSea/run_counter.txt
1 1 7200 20100105
2 7201 14400 7200=20100109
3 14401 21600 14400=20100114

Note this copies old memo.exe and xios_server.exe into the config directory. But these are replaced / updated by the run_nemo script

Edit run_nemo
vi run_nemo
export HOMEDIR= /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo        # Home Directory
...
export XIOSDIR=/work/n01/n01/jdha/ST/xios-1.0/

cp  XIOS_AMM60_nemo/submit_nemo.pbs XIOS_AMM60_nemo/EXP_NSea/submit_nemo.pbs
vi XIOS_AMM60_nemo/EXP_NSea/submit_nemo.pbs
Set a 5 min wall time.

# Copy in namelist files
cd XIOS_AMM60_nemo
cp namelist_ref EXP_NSea/.
cp namelist_cfg EXP_NSea/.

cp iodef.xml EXP_NSea/.
cp domain_def.xml EXP_NSea/.

# Add in restart files
ln -s /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_notradiff_long/RESTART/00014400 /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_NSea/RESTART/00014400

./run_nemo
3941432.sdb
IT RUN AND WAS KILLED BY THE WALL TIME LIMIT.

Housecleaning:
move unnecessary files in
/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo
to
/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/TMP

Change the queue to 20mins. Run_counter.txt OK To check TMP storage OK

jelt@eslogin006:/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_NSea
./run_nemo
3941652.sdb

sdb:
                                                            Req'd  Req'd   Elap
Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
--------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
3941652.sdb     jelt     standard AMM60_run     --   92 220    --  00:20 Q   --

IT WORKS

16 Sept 2016

Check output: AMM60_read_plot.ipynb Looks OK. Though has a non zero mean in this subdomain.
Plot zos - SSH, hourly

NEW BUILD
/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_NSea/OUTPUT/AMM60_1h_20100115_20100119_NorthSea1.nc

OLD BUILD (Karen’s executable)
/work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_NSea/OUTPUT/AMM60_1h_20100115_20100119_NorthSea1.nc

Old build and new build look the same.
 Hooray!
