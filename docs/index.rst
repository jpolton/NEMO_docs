.. jpolton's first docs documentation master file, created by
   sphinx-quickstart on Mon Oct 10 15:23:34 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to jpolton's first docs's documentation!
================================================

Contents:

.. toctree::
   :maxdepth: 2

   all-about-me

   AMM60_restarts_and_XIOS_dev

   3D_Harmonic_analysis

Introduction
============
One fine hat with a very small cat went to swim by the sea.

Test test test test test tes

1. one
2. two
#. thress
#. four


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

* Try an EXP_harmIT with Maria’s modifications with XML output for 3D tides. In progress (10 Oct 2016)
* Porting dev logging to GitHub https://github.com/jpolton/EXP_harmIT.git

PLANS:

* I need to spin up this model to get meaningful output. Perhaps switch it in for the ANChor runs? Talk to Maria
* Changes to field_def.xml  - with 3D harmonics no longer using Karen’s version. Need to propagate through notes.
* Spring neaps variation. Seasonal variation. Check notes elsewhere for plans
* Write some papers.


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
