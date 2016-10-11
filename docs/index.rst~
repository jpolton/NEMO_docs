.. jpolton's first docs documentation master file, created by
   sphinx-quickstart on Mon Oct 10 15:23:34 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to jpolton's first docs's documentation!
================================================

Contents:

.. toctree::
   :maxdepth: 2

   AMM60_restarts_and_XIOS_dev

   Compiling_AMM60

   3D_Harmonic_analysis

Introduction
============
This file is the launch page for different work flows.

**Need to add more stuff**



3D Harmonic analysis
====================

Logs at 3D Harmonic analysis

PATH: ``/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo``
``/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT``

PROGRESS:

* Successfully compiled Karen’s code.
* Ran for 20 mins in a clean new EXP config. Ran as EXP_NSea.
* Checked output is OK for NSea test: AMM60_read_plot.ipynb
* Successfully compiled and ran Karen's code with Maria’s 3D harmonics (though w/o key_diaharm). Outputted virtual moorings OK.
* Recompiled Maria’s 3D harmonics with key_diaharm
* Added in new field_def.xml variables for the 3D harmonics

CURRENT:

* Try an EXP_harmIT with Maria’s modifications with XML output for 3D tides. **In progress (10 Oct 2016)**
* **Porting dev logging to GitHub** https://github.com/jpolton/EXP_harmIT.git

PLANS:

* I need to spin up this model to get meaningful output. Perhaps switch it in for the ANChor runs? Talk to Maria
* Changes to field_def.xml  - with 3D harmonics no longer using Karen’s version. Need to propagate through notes.
* Spring neaps variation. Seasonal variation. Check notes elsewhere for plans
* Write some papers.


AMM60 June 2012 restarts and virtual mooring output
===================================================
Logs at AMM60 restarts and XIOS dev. Restarts on the new compiled code.

PATH: ``/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo``
``cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo``

PROGRESS:

#. Successfully compiled Karen’s code.
#. Generate the XML code using python notebook: ``jcomp_tools_dev/AMM60_build_iodef_from_latlon.ipynb``
#. Successfully generated virtual mooring test data for early June 2012 using Karen’s restart: ``/work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/EXPD376/``
#. Generated virtual moorings for Jo. Got the green light to proceed with high resolution data.

CURRENT:

* **Fine resolution spacing virtual moorings IN PROGRESS (3305 moorings) - 3 Oct 2016**

``cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings``

ISSUES:

#. **CHECK THAT THE I/O PRESSURE IS OK WITH THE 20min wall time**

PLANS:

#. Run June 2012 with full list of moorings
#. Migrate tech notes to NEMO skills
#. Compress output in the finish script?
#. Other output:

  * Slowly varying stratification
  * Baines forcing
  * ...





Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
