======================
NEMO runs: where am I?
======================
.. jpolton's first docs documentation master file, created by
   sphinx-quickstart on Mon Oct 10 15:23:34 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.



Pages made:

.. toctree::
   :maxdepth: 2

   Generate_AMM60_mesh_and_mask_files_from_stable_build

   Testing_a_new_compilation_with_NSea_experiment

   AMM60_restarts_and_XIOS_dev

   Compiling_AMM60

   3D_Harmonic_analysis

   Debugging_XIOS_in_AMM7

   AMM60_runs_for_ANChor

   AMM7_3D_Harmonic_analysis

Introduction
============
This file is the launch page for different work flows.

**Need to add more stuff**

`External page <http://nemo-docs.readthedocs.io/en/latest/?#>`_



3D Harmonic analysis
====================

Logs at `3D Harmonic analysis <3D_Harmonic_analysis.html>`_

PATH:
 ``/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo``
 ``/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT``

PROGRESS:

* Successfully compiled Karen’s code.
* Ran for 20 mins in a clean new EXP config. Ran as EXP_NSea.
* Checked output is OK for NSea test: ``AMM60_read_plot.ipynb``
* Successfully compiled and ran Karen's code with Maria’s 3D harmonics (though w/o key_diaharm). Outputted virtual moorings OK.
* Recompiled Maria’s 3D harmonics with key_diaharm
* Added in **new field_def.xml** variables for the 3D harmonics
* Ported dev logging to GitHub: https://github.com/jpolton/EXP_harmIT.git
* Edits to ``namelist_cfg: nnit???_han``
* Exceed walltime: 2day in 5mins (1.5days in 5mins)
* 5 day (19mins) simulation with 3D harmonic output **WORKS (22 Oct 2016)**
* Found bug in namelist_cfg edit with nn_write
* Added ``ln_dia25h`` and edited harmonic constituents wanted in ``namelist_cfg``.
* Added 25hour diagnostics to ``field_def.xml``
* Trying 25hour output diagnostics with 3D harmonics. It appears that the dia25h does activate.


CURRENT:

* Try `mapping AMM60 to v3.6_STABLE from AMM7 <AMM7_3D_Harmonic_analysis.index>`_ .  **PENDING 16 Jan 2017**


PLANS:

* I need to spin up this model to get meaningful output. Perhaps switch it in for the ANChor runs? Talk to Maria
* Changes to field_def.xml  - with 3D harmonics no longer using Karen’s version. Need to propagate through notes.
* Spring neaps variation. Seasonal variation. Check notes elsewhere for plans
* Write some papers.


AMM60 June 2012 restarts and virtual mooring output
===================================================
Logs at `AMM60 restarts and XIOS dev <AMM60_restarts_and_XIOS_dev.html>`_. Restarts on the new compiled code.

PATH: ``/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo``
``cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo``

PROGRESS:

#. Successfully compiled Karen’s code.
#. Generate the XML code using python notebook: ``jcomp_tools_dev/AMM60_build_iodef_from_latlon.ipynb``
#. Successfully generated virtual mooring test data for early June 2012 using Karen’s restart: ``/work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/EXPD376/``
#. Generated virtual moorings for Jo. Got the green light to proceed with high resolution data.
#. Found a bug in the ``iodef.xml`` file using AMM7
#. 3305 XML files create memory problem
#. 3305 moorings in 1 file completed but no XML output. (with or without XML typo)
#. 2 moorings in 1 files works and outputs (5days takes 20mins)
#. 999 moorings in 1 file works and outputs (27hours takes 20mins) **ACTION. Check output. I LOST THE DATA**
#. 100 moorings each in 34 files (OOM after 25.1hrs in 10mins. No OUTPUT) **BUG IN iodef.xml**
#. 100 moorings each in 34 files (doubled XIOS processors) **Netcdf issues**
#. Found error in ``namelist_cfg`` with ``nn_write`` not being edited
#. 100 moorings each in 34 files (fewer XIOS processors per node) **PENDING (21 Oct 2016)**
#. 100+5 moorings each in 1+1 files (fewer XIOS processors per node) **PENDING (21 Oct 2016)**
#. 100+5 moorings each in 1+1 files (fewer XIOS processors per node). EXP_SBmoorings3. In old executable. At least 40mins for 1 day **Wall time exceeded**
#. 100+5 moorings each in 1+1 files (x10 XIOS processors per node). EXP_SBmoorings3. In old executable. **EXCEEDED RESOURCE ALLOWED**
#. 100+5 moorings each in 1+1 files. 1pt moorings. (fewer XIOS processors per node). EXP_SBmoorings. 20min queue **BROKE**
#. 3 moorings in 2 files works in AMM7.
#. Switching to outputting 3d array for moorings output usugn a 2d mask array for moorings locations
#. Shelf break mooring subdomain. Output 3D arrays with a mooring mask applied.
#. June 2012 simulation finished
#. off-line compression on netcdf output. Gzipping saves approximately a factor of 2 space.

CURRENT:
cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings


ACTIONS: (16 Jan 2016)

* Tidy up and close this work Object.


ISSUES:

* Not succeeded in outputting 3305 1d moorings. An I/O problem?
* Outputted using masked 3D spatial arrays.

PLANS:

* Migrate tech notes to NEMO skills
* Gzip output in the finish script?
* Other output:
  * Slowly varying stratification
  * Baines forcing
  * ...



NEMO skills
===========

  Aim: document basic functionality in getting stuff setup and working.

  * `Compiling AMM60 <Compiling_AMM60.html>`_.
  * `Generate mesh and mask files on short queue <https://www.evernote.com/shard/s523/nl/2147483647/f0149755-b99c-4f5c-a488-bd2940e16d20/>`_ (originally for AMM60 ANChor output). ``EXP_NSea_mesh``. **Doesn’t actually work**. Tried reducing tilmestep to 6s. No good. (22 Sept 16)
  * `Configure AMM60 on the short queue <https://www.evernote.com/shard/s523/nl/2147483647/e0445ece-518f-4a5f-aff5-6ff5df8a491e/>`_ - though this **doesn’t actually work…**
  * `Generate mesh and mask files on long queue <https://www.evernote.com/shard/s523/nl/2147483647/300e814a-5c06-4677-9c2e-d76e02133a11/>`_ (Karen’s codebase. Restart. Fresh compile. nn_msh=[1,3]). **Doesn’t work**
  * `Generate AMM60 mesh and mask files from stable build <Generate_AMM60_mesh_and_mask_files_from_stable_build.html>`_ ``(EXP_meshgen) qsub nc_mesh_build.pbs —> mesh_mask.nc``. **WORKS!**
  * Compile ``-g -traceback`` debug version - there are some notes somewhere, though there is not much to say.
  * `Debugging XML sensitivities in the short queue with AMM7 <Debugging_XIOS_in_AMM7.html>`_

  PATH:
  ``/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/`` - **DIDN’T WORK**
  ``/work/n01/n01/jelt/NEMO/nemo_v3_6_STABLE_r6939/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_meshgen`` - **DID WORK (22 Sept 2016)**

  PROGRESS:

  * Compiled code (Karen’s code base)
  * Built AMM60 on short queue (as a cold start) - does not run
  * Compile code in debug ``-g -traceback`` mode
  * Built v3.6_STABLE. Restart with ``nn_msh = 1 —> mesh_mask.nc``

  CURRENT:

  Issues:

  * Generating meshes is a problem with Karen’s code. It worked with a recent v3.6_STABLE.
  * Can’t run short queue AMM60

  Conclusions

  * Can generate ``mesh_mask.nc`` file with new stable code but not Karen’s code.
  * Cannot run on the short queue (SEG FAULTS). It might work on v3.6_STABLE

  PLANS:

  * **Try short queue with v3.6_STABLE**
  * **Write compiling debug executable as a note**
  * **Tidy up short queue pages.**
  * Compress output in the finish script?


ANChor runs
===========

| dev logs at `AMM60 runs for ANChor - dev <https://www.evernote.com/shard/s523/nl/2147483647/0e6c9337-5fa1-4039-9af8-00e7e02f2daf/>`_
| Production logs at `AMM60 runs for ANChor <AMM60_runs_for_ANChor.html>`_

PATH:
``/work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/``

PROGRESS:

* Running with hourly velocity output with vertical viscosity AVM
* mesh files generated with `Generate AMM60 mesh and mask files from stable build <Generate_AMM60_mesh_and_mask_files_from_stable_build.html>`_
* Have output through to the end of Aug 2012 (27th)
* Generated hourly output for North Sea: Jan 2010 - Nov 2013

CURRENT:

* Finished integration production runs `AMM60 runs for ANChor <AMM60_runs_for_ANChor.html>`_
  ``cd  /work/n01/n01/jelt/NEMO/NEMOGCM/CONFIG/AMM60smago/EXP_NSea`` (22 Oct 2016).

ACTIONS:

* gzipping 2012 and 2013 **Submitted (16 Jan 2017)**

PLANS:

* Gzip compression of output in the finish script



AMM60 in v3.6_STABLE
====================
Updating Karen’s code base

PROGRESS:

* downloaded and compiled v3.6_STABLE. `Fresh build with v3.6_STABLE <https://www.evernote.com/shard/s523/nl/2147483647/408b131e-fddf-4fee-94ec-d4f0671f0e21/>`_
* generated AMM60 mesh files. `Generate AMM60 mesh and mask files from stable build <Generate_AMM60_mesh_and_mask_files_from_stable_build.html>`_

CURRENT:

* Have not updated AMM60 to v3.6_STABLE. `Compiling AMM60 from v3.6_STABLE <https://www.evernote.com/shard/s523/nl/2147483647/cc65c445-f129-4db7-9310-b976c49645b0/>`_.  **In progress**
* Copied Maria's 3D harmonics version of AMM7 running on v3.6_STABLE: `AMM7 3D Harmonic analysis <AMM7_3D_Harmonic_analysis.html>`_  **IN PROGRESS (11 Jan 17)**

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
