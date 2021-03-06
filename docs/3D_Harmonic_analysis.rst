====================
3D Harmonic Analysis
====================

PATH::

  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT

Method:

* First follow `Compiling AMM60 <Compiling_AMM60.html>`_. **15 Sept 2016**
* Test code on NSea simulations.Testing a new compilation with NSea experiment. New build ssh looks like old build ssh. Check output: ``AMM60_read_plot.ipynb`` Looks OK. **16 Sept 2016**
* Make a clean EXPeriment directory EXP_harmIT. In clean build configuration. **20 Sept 2016**
  ``/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/EXP_harmIT``


----

load modules::

  module add cray-hdf5-parallel
  module load  cray-netcdf-hdf5parallel
  module swap PrgEnv-cray PrgEnv-intel

Make the configuration directory (with default options which generates a ``cfg.txt`` file with options: ``XIOS_AMM60_nemo OPA_SRC LIM_SRC_2 NST_SRC``)::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG
  ./makenemo -n XIOS_AMM60_nemo_harmIT -m XC_ARCHER_INTEL -j 10

Copy MY_SRC code from Karen::

  rsync -uartvL --delete /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/MY_SRC/ /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/MY_SRC

Add MY_SRC code from Maria::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/MY_SRC
  mv diaharm2.F90 diaharm_.F90_kariho40
  #mv dia25h.F90 dia25h.F90_kariho40
  cp /work/n01/n01/mane1/V3.6_ST/NEMOGCM/NEMO/OPA_SRC/DIA/diaharm.F90 diaharm.F90_mane1

Maria suggested I need the following but it breaks the compilation so for now comment it out.
``#cp /work/n01/n01/mane1/V3.6_ST/NEMOGCM/NEMO/OPA_SRC/DIA/dia25h.F90 .``

Create a new ``diaharm.F90`` that merges Karen and Maria’s. Start with Maria’s::

  cp diaharm.F90_mane1 diaharm.F90
  sdiff diaharm.F90_mane1 diaharm2.F90_kariho40

  mane1                                                            kariho40
  bold is the new / accepted version

        INTEGER :: jh, nhan, jl       |       INTEGER :: jh, nhan, jk, ji

        DO jh=1,jpmax_harmo       |       DO jk=1,jpmax_harmo
           DO jl=1,jpmax_harmo       |         DO ji=1,jpmax_harmo
              IF(TRIM(tname(jh)) == Wave(jl)%cname_tide) THEN   |             IF(TRIM(tname(jk)) == Wave(ji)%cname_tide) THEN
                 nb_ana=nb_ana+1               nb_ana=nb_ana+1
              ENDIF             ENDIF

        DO jh=1,nb_ana       |       DO jk=1,nb_ana
         DO jl=1,jpmax_harmo       |       DO ji=1,jpmax_harmo
            IF (TRIM(tname(jh)) .eq. Wave(jl)%cname_tide) THEN  |           IF (TRIM(tname(jk)) .eq. Wave(ji)%cname_tide) THEN
               name(jh) = jl       |             name(jk) = ji
               EXIT             EXIT
            END IF           END IF

        ! Initialize temporary arrays:       ! Initialize temporary arrays:
        ! ----------------------------       ! ----------------------------
        ALLOCATE( ana_temp(jpi,jpj,2*nb_ana,5,jpk) )       |       ALLOCATE( ana_temp(jpi,jpj,2*nb_ana,3) )
        ana_temp(:,:,:,:,:) = 0._wp       |       ana_temp(:,:,:,:) = 0._wp

        INTEGER  :: ji, jj, jh, jc, nhc ,jk       |       INTEGER  :: ji, jj, jh, jc, nhc

  ! ssh, ub, vb are stored at the last level of 5d array       |

  …3D stuff only here that we keep
  SUBROUTINE dia_harm_end

        INTEGER :: ji, jj, jh, jc, jn, nhan, jl ,jk       |       INTEGER :: ji, jj, jh, jc, jn, nhan, jl...
        ALLOCATE( out_eta(jpi,jpj,jpk,2*nb_ana),   &       <
           &      out_u  (jpi,jpj,jpk,2*nb_ana),   &       <
           &      out_v  (jpi,jpj,jpk,2*nb_ana),   &       <
           &      out_w  (jpi,jpj,jpk,2*nb_ana),   &       <
        >       ALLOCATE( out_eta(jpi,jpj,2*nb_ana),   &
        >         &      out_u  (jpi,jpj,2*nb_ana),   &
                            >         &      out_v  (jpi,jpj,2*nb_ana)  )

           &      out_dzi(jpi,jpj,jpk,2*nb_ana) )       <
        ! density and Elevation:       |       ! Elevation:
     DO jk=1,jpk       <
                    ztmp4(kun)=ana_temp(ji,jj,kun,1,jk)       |                   ztmp4(kun)=ana_temp(ji,jj,kun,1)

                 out_eta(ji,jj,jk,jh       ) = X1 * tmask_i(ji, |               out_eta(ji,jj,jh       ) = X1 * tmask_i(ji,jj)
                 out_eta(ji,jj,jk,jh+nb_ana) = X2 * tmask_i(ji, |               out_eta(ji,jj,jh+nb_ana) = X2 * tmask_i(ji,jj)
        ! u-component of velocity       |       ! ubar:


----

On reflection the bits kept from Karen’s version are not helpful. They just seem to be a straight label swap and does not achieve anything.

----

Copy in the compiler flags file::

  cp /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/cpp_AMM60smago.fcm /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/cpp_XIOS_AMM60_nemo_harmIT.fcm

Check /add the key_diaharm::

 vi  cpp_XIOS_AMM60_nemo_harmIT.fcm
 bld::tool::fppkeys     key_ldfslp key_iomput key_mpp_mpi key_netcdf4 key_tide key_bdy key_jdha_init key_dynspg_ts key_vvl key_zdfgls key_dynldf_smag key_traldf_smag key_traldf_c3d    key_dynldf_c3d  key_diaharm

Compile with ``XC_ARCHER_INTEL``::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG
  ./makenemo -n XIOS_AMM60_nemo_harmIT -m XC_ARCHER_INTEL -j 10 clean
  ./makenemo -n XIOS_AMM60_nemo_harmIT -m XC_ARCHER_INTEL -j 10

**WORKED! IT COMPILES**

----

**INSERT 3 Oct**::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/MY_SRC
  cp /work/n01/n01/mane1/V3.6_ST/NEMOGCM/NEMO/OPA_SRC/DIA/dia25h.F90 ../MY_SRC/.
  cp /work/n01/n01/mane1/V3.6_ST/NEMOGCM/NEMO/OPA_SRC/DIA/diainsitutem.F90 ../MY_SRC/.

Recompile::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG
  ./makenemo -n XIOS_AMM60_nemo_harmIT -m XC_ARCHER_INTEL -j 10 clean
  ./makenemo -n XIOS_AMM60_nemo_harmIT -m XC_ARCHER_INTEL -j 10

Logs::

  ...
  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/BLD/ppsrc/nemo/dia25h.f90(24): error #6580: Name in only-list does not exist.   [EN]
     USE zdf_oce, ONLY: en
  ----------------------^
  ...
  Build failed on Mon Oct  3 15:56:07 2016.

  Build failed in dia25h.F90
  ...
     USE zdf_oce, ONLY: en
  ...

Compare ``zdf_oce.F90``::

  diff  /work/n01/n01/mane1/V3.6_ST/NEMOGCM/NEMO/OPA_SRC/ZDF/zdf_oce.F90 /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/NEMO/OPA_SRC/ZDF/zdf_oce.F90
  45,47d44
  <    REAL(wp), PUBLIC, ALLOCATABLE, SAVE, DIMENSION(:,:,:) ::   avt_k , avm_k  ! not enhanced Kz
  <    REAL(wp), PUBLIC, ALLOCATABLE, SAVE, DIMENSION(:,:,:) ::   avmu_k, avmv_k ! not enhanced Kz
  <    REAL(wp), PUBLIC, ALLOCATABLE, SAVE, DIMENSION(:,:,:) ::   en              !: now turbulent kinetic energy   [m2/s2]
  51c48
  <    !! $Id: zdf_oce.F90 6204 2016-01-04 13:47:06Z cetlod $
  ---
  >    !! $Id: zdf_oce.F90 5038 2015-01-20 14:26:13Z jamesharle $
  65,68c62
  <          &     avmv  (jpi,jpj,jpk), avt   (jpi,jpj,jpk)      ,      &
  <          &     avt_k (jpi,jpj,jpk), avm_k (jpi,jpj,jpk)      ,      &
  <          &     avmu_k(jpi,jpj,jpk), avmv_k(jpi,jpj,jpk)      ,      &
  <          &     en    (jpi,jpj,jpk), STAT = zdf_oce_alloc )
  ---
  >          &     avmv(jpi,jpj,jpk), avt(jpi,jpj,jpk)           , STAT = zdf_oce_alloc )

**I.e. Maria has a newer version of** ``zdf_oce.F90``. Copy it::

  cp  /work/n01/n01/mane1/V3.6_ST/NEMOGCM/NEMO/OPA_SRC/ZDF/zdf_oce.F90 /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/MY_SRC/zdf_oce.F90

Also there are knock ons in ``zdfgls.F90``. Copy it (accidentally overwrote the OPA_SRC/ZDF/ version)::

  # cp  /work/n01/n01/mane1/V3.6_ST/NEMOGCM/NEMO/OPA_SRC/ZDF/zdfgls.F90  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/NEMO/OPA_SRC/ZDF/zdfgls.F90

  cp  /work/n01/n01/mane1/V3.6_ST/NEMOGCM/NEMO/OPA_SRC/ZDF/zdfgls.F90  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/MY_SRC/zdfgls.F90

Recompile::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG
  ./makenemo -n XIOS_AMM60_nemo_harmIT -m XC_ARCHER_INTEL -j 10 clean
  ./makenemo -n XIOS_AMM60_nemo_harmIT -m XC_ARCHER_INTEL -j 10

**IT WORKED!**


Make a clean EXPeriment: EXP_harmIT
===================================
**Note that this EXP requires a modified version of field_def.xml. Previously we used Karen’s reference version. This requires a modification to run_nemo**

Move to configuration directory and create EXPeriment directory::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT
  mkdir EXP_harmIT

Copy run script into job directory::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings/run_nemo EXP_harmIT/run_nemo
  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings/submit_nemo.pbs EXP_harmIT/submit_nemo.pbs

Make a SHARED directory, and copy field_def.xml to it::

  mkdir /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/SHARED
  cp /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/SHARED/field_def.xml  /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/SHARED/.

Edit run script (Note I only want one restart at this stage)::

  vi EXP_harmIT/run_nemo
  export RUNNAME=EXP_harmIT
  export YEARrun='2012'
  export HOMEDIR=/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT
  exportnrestart_max=2#31 (For one submission this number must equal the number of lines in run_counter.txt)
  Edit the SHAREDDIR path to be mine and not Karen's
  export SHAREDDIR=$HOMEDIR/../SHARED                     # Config directory

  # edit where the namelist_cfg is updated to include harmonic analysis
  ...
  273c\
  nit000_han = "$nn_it000"
  274c\
  nitend_han = "$nitend" " $JOBDIR/namelist_cfg > $WDIR/namelist_cfg

Edit submit script. Extended wall time to see how long it takes::

  vi submit_nemo.pbs
  #PBS -N AMM60_harmIT
  #PBS -l walltime=00:20:00

Copy iodef.xml into job directory::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings/iodef.xml EXP_harmIT/iodef.xml

Add in tidal diagnostics (copied from   ``/work/n01/n01/mane1/AMM7_w/iodef.xml``). Comment out most, just to get it working::

  vi EXP_harmIT/iodef.xml
      <file_group id="1d" output_freq="1d"  output_level="10" enabled=".TRUE." > <!-- 1d files -->
  ...

        <file id="file8" name_suffix="_Tides" description="tidal harmonics" >
          <field field_ref="e3t"  />
          <field field_ref="gdept"/>
          <field field_ref="M2x_ro"      name="M2x_ro"  long_name="M2 ro   real part"                      />
          <field field_ref="M2y_ro"      name="M2y_ro"  long_name="M2 ro  imaaginary part"                  />
          <field field_ref="M2x_u"        name="M2x_u"    long_name="M2 current bcl-baro i-axis harmonic real "      />
          <field field_ref="M2y_u"        name="M2y_u"    long_name="M2 current bcl-baro i-axis harmonic imaginary "  />
          <field field_ref="M2x_v"        name="M2x_v"    long_name="M2 current bcl-baro j-axis harmonic real "      />
          <field field_ref="M2y_v"        name="M2y_v"    long_name="M2 current bcl-baro j-axis harmonic imaginary "  />
          <field field_ref="M2x_w"        name="M2x_w"    long_name="M2 current vertical harmonic real part "      />
          <field field_ref="M2y_w"        name="M2y_w"    long_name="M2 current vertical imaginary part "  />
          <field field_ref="M2x_SSH"      name="M2x_SSH"  long_name="M2 ro   real part"                      />
          <field field_ref="M2y_SSH"      name="M2y_SSH"  long_name="M2 ro  imaaginary part"                  />
          <field field_ref="M2x_u2d"      name="M2x_u2d"  long_name="M2 current baro i-axis harmonic real "      />
          <field field_ref="M2y_u2d"      name="M2y_u2d"  long_name="M2 current baro i-axis harmonic imaginary "  />
          <field field_ref="M2x_v2d"      name="M2x_v2d"  long_name="M2 current baro j-axis harmonic real "      />
          <field field_ref="M2y_v2d"      name="M2y_v2d"  long_name="M2 current baro j-axis harmonic imaginary "  />
          <field field_ref="M2x_dzi"      name="M2x_dzi"  long_name="M2 isopycnal elevations harmonic real part "  />
          <field field_ref="M2y_dzi"      name="M2y_dzi"  long_name="M2 isopycnal elevations imaginary part "  />
          <field field_ref="M2x_tabx"      name="M2x_tabx"  long_name="M2 bottom shear stress i-axis  real "      />
          <field field_ref="M2y_tabx"      name="M2y_tabx"  long_name="M2 bottom shear stress i-axes  imaginary "  />
          <field field_ref="M2x_taby"      name="M2x_taby"  long_name="M2 bottom shear stress j-axes  real "      />
          <field field_ref="M2y_taby"      name="M2y_taby"  long_name="M2 bottom shear stress j-axis  imaginary "  />
  <!--
          <field field_ref="S2x_ro"      name="S2x_ro"  long_name="S2 ro   real part"                      />
          <field field_ref="S2y_ro"      name="S2y_ro"  long_name="S2 ro  imaaginary part"                  />
          <field field_ref="S2x_u"        name="S2x_u"    long_name="S2 current bcl-baro i-axis harmonic real "      />
          <field field_ref="S2y_u"        name="S2y_u"    long_name="S2 current bcl-baro i-axis harmonic imaginary "  />
          <field field_ref="S2x_v"        name="S2x_v"    long_name="S2 current bcl-baro j-axis harmonic real "      />
          <field field_ref="S2y_v"        name="S2y_v"    long_name="S2 current bcl-baro j-axis harmonic imaginary "  />
          <field field_ref="S2x_w"        name="S2x_w"    long_name="S2 current vertical harmonic real part "      />
          <field field_ref="S2y_w"        name="S2y_w"    long_name="S2 current vertical imaginary part "  />
          <field field_ref="S2x_SSH"      name="S2x_SSH"  long_name="S2 ro   real part"                      />
          <field field_ref="S2y_SSH"      name="S2y_SSH"  long_name="S2 ro  imaaginary part"                  />
          <field field_ref="S2x_u2d"      name="S2x_u2d"  long_name="S2 current baro i-axis harmonic real "      />
          <field field_ref="S2y_u2d"      name="S2y_u2d"  long_name="S2 current baro i-axis harmonic imaginary "  />
          <field field_ref="S2x_v2d"      name="S2x_v2d"  long_name="S2 current baro j-axis harmonic real "      />
          <field field_ref="S2y_v2d"      name="S2y_v2d"  long_name="S2 current baro j-axis harmonic imaginary "  />
          <field field_ref="S2x_dzi"      name="S2x_dzi"  long_name="S2 isopycnal elevations harmonic real part "  />
          <field field_ref="S2y_dzi"      name="S2y_dzi"  long_name="S2 isopycnal elevations imaginary part "  />
          <field field_ref="S2x_tabx"      name="S2x_tabx"  long_name="S2 bottom shear stress i-axis  real "      />
          <field field_ref="S2y_tabx"      name="S2y_tabx"  long_name="S2 bottom shear stress i-axes  imaginary "  />
          <field field_ref="S2x_taby"      name="S2x_taby"  long_name="S2 bottom shear stress j-axes  real "      />
          <field field_ref="S2y_taby"      name="S2y_taby"  long_name="S2 bottom shear stress j-axis  imaginary "  />
  —>
        </file>
  ...

Edit ``field_def.xml`` in SHARED directory. Copy and paste from  ``/work/n01/n01/mane1/AMM7_w/field_def.xml``::

  vi /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/SHARED/field_def.xml

     <field_group id="Tides_T" grid_ref="grid_T_2D" operation="once" >
         <!-- tidal composante -->
         <field id="M2x_ro"          long_name="M2 ro/roa  harmonic real part "    unit="none" grid_ref="grid_T_3D"    />
         <field id="M2y_ro"          long_name="M2 ro/roa  harmonic imaginary part" unit="none" grid_ref="grid_T_3D"  />
         <field id="S2x_ro"          long_name="S2 ro/roa  harmonic real part "    unit="none" grid_ref="grid_T_3D"  />
         <field id="S2y_ro"          long_name="S2 ro/roa  harmonic real part "    unit="none" grid_ref="grid_T_3D"  />
         <field id="K1x_ro"          long_name="K1 ro/roa harmonic real part "      unit="none" grid_ref="grid_T_3D"  />
         <field id="K1y_ro"          long_name="K1 ro/roa harmonic imaginary part"  unit="none" grid_ref="grid_T_3D"  />
         <field id="O1x_ro"          long_name="O1 ro/roa harmonic real part "      unit="none" grid_ref="grid_T_3D"  />
         <field id="O1y_ro"          long_name="O1 ro/roa harmonic real part "      unit="none" grid_ref="grid_T_3D"  />
         <field id="Q1x_ro"          long_name="Q1 ro/roa harmonic real part "      unit="none" grid_ref="grid_T_3D"  />
         <field id="Q1y_ro"          long_name="Q1 ro/roa harmonic imaginary part"  unit="none" grid_ref="grid_T_3D"    />
         <field id="S1x_ro"          long_name="S1 ro/roa harmonic real part "      unit="none" grid_ref="grid_T_3D"  />
         <field id="S1y_ro"          long_name="S1 ro/roa harmonic imag part "      unit="none" grid_ref="grid_T_3D"  />
         <field id="M4x_ro"          long_name="M4 ro/roa harmonic real part "      unit="none" grid_ref="grid_T_3D"  />
    ...
         <field id="M2x_dzi"          long_name="M2 isopycnal elevation harmonic real part "      unit="m"      />
         <field id="M2y_dzi"          long_name="M2 isopycnal elevation harmonic imaginary part " unit="m"      />
         <field id="S2x_dzi"          long_name="S2 isopycnal elevation harmonic real part "      unit="m"      />
         <field id="S2y_dzi"          long_name="S2 isopycnal elevation harmonic imaginary part " unit="m"      />
         <field id="N2x_dzi"          long_name="N2 isopycnal elevation harmonic real part "      unit="m"      />
         <field id="N2y_dzi"          long_name="N2 isopycnal elevation harmonic imaginary part " unit="m"      />
         <field id="K2x_dzi"          long_name="K2 isopycnal elevation harmonic real part "      unit="m"      />
         <field id="K2y_dzi"          long_name="K2 isopycnal elevation harmonic imaginary part " unit="m"      />
         <field id="K1x_dzi"          long_name="K1 isopycnal elevation harmonic real part "      unit="m"      />
         <field id="K1y_dzi"          long_name="K1 isopycnal elevation harmonic imaginary part " unit="m"      />
         <field id="O1x_dzi"          long_name="O1 isopycnal elevation harmonic real part "      unit="m"      />
         <field id="O1y_dzi"          long_name="O1 isopycnal elevation harmonic imaginary part " unit="m"      />
         <field id="Q1x_dzi"          long_name="Q1 isopycnal elevation harmonic real part "      unit="m"      />
         <field id="Q1y_dzi"          long_name="Q1 isopycnal elevation harmonic imaginary part " unit="m"      />
         <field id="P1x_dzi"          long_name="P1 isopycnal elevation harmonic real part "      unit="m"      />
         <field id="P1y_dzi"          long_name="P1 isopycnal elevation harmonic imaginary part " unit="m"      />
         <field id="M4x_dzi"          long_name="M4 isopycnal elevation harmonic real part "      unit="m"      />
         <field id="M4y_dzi"          long_name="M4 isopycnal elevation harmonic imaginary part " unit="m"      />
         <field id="Mfx_dzi"          long_name="Mf isopycnal elevation harmonic real part "      unit="m"      />
         <field id="Mfy_dzi"          long_name="Mf isopycnal elevation harmonic imaginary part " unit="m"      />
         <field id="Mmx_dzi"          long_name="Mm isopycnal elevation harmonic real part "      unit="m"      />
         <field id="Mmy_dzi"          long_name="Mm isopycnal elevation harmonic imaginary part " unit="m"      />
         <field id="S1x_dzi"          long_name="S1 isopycnal elevation harmonic real part "      unit="m"      />
         <field id="S1y_dzi"          long_name="S1 isopycnal elevation harmonic imaginary part " unit="m"      />

     </field_group>


Copy domain_def.xml into job directory::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings/domain_def.xml EXP_harmIT/domain_def.xml

Copy finish_nemo.sh into job directory::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings/finish_nemo.sh EXP_harmIT/finish_nemo.sh

Link restart files::

  mkdir EXP_harmIT/RESTART
  ln -s  /work/n01/n01/kariho40/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/AMM60smago/EXPD376/RESTART/01264320  EXP_harmIT/RESTART/.

Create ``run_counter.txt`` into job directory. Start from 23:30 31 May 2012 (I think). Run for 5 days::

  vi EXP_harmIT/run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520

Edit run_counter,  to run for two days (2*1440 = 2880 minutes)::

  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1267200

Copy in namelists::

  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings/namelist_ref EXP_harmIT/.
  cp /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo/EXP_SBmoorings/namelist_cfg EXP_harmIT/.



Try Two Simultaneous experiments:
=================================

First Run
=========

Submit run (**5min wall time**). Fiddled with ``iodef.xml`` to **output** HOURLY::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/EXP_harmIT
  ./run_nemo
  3972846.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  3972846.sdb     jelt     standard AMM60_harm    --   92 220    --  00:05 Q   -- <--Does it WORK?
  OUTPUT SHOULD BE 3D harmonics, outputted HOURLY over 2 days

| **Broke.**
| **Try cleaning up** ``iodef.xml`` (remove comments):
| ``cp iodef.xml ifdef.xml_tmp``
| Several bugs. Resubmit

Trim ``run_counter.txt``. Resubmit::

  ./run_nemo
  3977937.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  3977937.sdb     jelt     standard AMM60_harm    --   92 220    --  00:05 Q   — <— DOES THIS WORK.
  PREVIOUSLY ALWAYS GOT CORE DUMPS.
  OUTPUT SHOULD BE 3D harmonics, outputted hourly over 2 days (7 Oct 2016)

Hmm something went wrong::

  cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/EXP_harmIT

| **Something went wrong in field_def.xml.**
| Edit the comment tags to clean up the closure of comments. copy edits back to
| ``EXP_harmIT> mv WDIR/field_def.xml ../../SHARED/field_def.xml``

Trim run_counter.txt and resubmit::

  3982223.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  3982223.sdb     jelt     standard AMM60_harm    --   92 220    --  00:05 Q   --<— DOES THIS WORK.
  PREVIOUSLY ALWAYS GOT CORE DUMPS.
  OUTPUT SHOULD BE 3D harmonics, outputted hourly over 2 days (10 Oct 2016)

This WORKED. Resubmit full 3D harmonics list in ``iodef.xml`` on a 20minute job.

**BROKE. NO OUTPUT. RAN TO WALLTIME.**

Check output log::

  less ocean.output
  ...
  dia_harm_init: Tidal harmonic analysis initialization
   ~~~~~~~
   First time step used for analysis:  nit000_han=            1
   Last  time step used for analysis:  nitend_han=           75
   Time step frequency for harmonic analysis:  nstep_han=           15

   ===>>> : E R R O R
           ===========

   dia_harm_init : nit000_han must be greater than nit000

| This may well have broken the model.
| Fix and resubmit on a **5 minute** job on a **two day** simulation.

Edit ``run_counter.txt`` to run for two days::

  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520

Edit ``namelist_cfg`` harmonic analysis variables::

  &nam_diaharm   !   Harmonic analysis of tidal constituents ('key_diaharm')
  !-----------------------------------------------------------------------
     nit000_han = 1         ! First time step used for harmonic analysis
     nitend_han = 75        ! Last time step used for harmonic analysis
     nstep_han  = 15        ! Time step frequency for harmonic analysis

Change into::

  &nam_diaharm   !   Harmonic analysis of tidal constituents ('key_diaharm')
  !-----------------------------------------------------------------------
     nit000_han = 1264321         ! First time step used for harmonic analysis
     nitend_han = 1271520        ! Last time step used for harmonic analysis
     nstep_han  = 15        ! Time step frequency for harmonic analysis

**This needs to be added to the** ``run_nemo`` **script**

Edit wall time::

  vi submit_nemo.pbs
  ..
  #PBS -l walltime=00:05:00
  ..

Submit::

  ./run_nemo
  3984287.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  3984287.sdb     jelt     standard AMM60_harm    --   92 220    --  00:05 Q   -- <— DOES THIS WORK?
  EXPECT TWO DAYS OF 3D HARMONIC OUTPUT (11 Oct 2016)

  YES. But it hit the 5 min walltime limit. Though the harmonic output was started.

::

  less time.step
  1266175

This is 30.9 hours after the start. Hence 5mins wall-time --> 1.5days simulation.
Hence 31days simulation requires 103 mins wall time.

*First try and complete the two day simulation with 10mins walltime.*
First try and complete the **five day** simulation with **20mins** walltime.

Edit walltime::

  vi submit_nemo.pbs
  #PBS -l walltime=00:20:00

Edit ``run_counter.txt``::

  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520  # THIS IS A 5 DAY SIMULATION!!

Submit::

  ./run_nemo
  3985531.sdb

  sdb:
                                                              Req'd  Req'd   Elap
  Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
  --------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
  3985531.sdb     jelt     standard AMM60_harm    --   92 220    --  00:20 Q   --

**EXPECT hourly 3D harmonics from 5 day simulation (11 Oct 2016)**

``cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/EXP_harmIT``




Check output::



  ncdump -h WDIR/AMM60_1h_20120601_20120605_Tides.nc

| *Previously* This looks fine with 72 hourly outputs.
| Now resubmitted with a 20min wall time.

----

**12 Oct 2016**

| ``run_counter.txt`` ready for next run.
| OUTPUT written, 65Gb file: ``AMM60_1h_20120601_20120605_Tides.nc``
| logs seem fine.

Check output::

  module load cray-netcdf
  ncdump -h OUTPUT/AMM60_1h_20120601_20120605_Tides.nc

Stuff is there **Action** IS IT READABLE / PLOTTABLE?

NO!

ferret::

  yes? use AMM60_1h_20120601_20120605_Tides.nc
  yes? sh da
       currently SET data sets:
      1> ./AMM60_1h_20120601_20120605_Tides.nc  (default)
   name     title                             I         J         K         L         M         N
   NAV_LAT_GRID_T
            Latitude                         1:1120    1:1440    ...       ...       ...       ...
   NAV_LON_GRID_T
            Longitude                        1:1120    1:1440    ...       ...       ...       ...
   NAV_LAT_GRID_U
            Latitude                         1:1120    1:1440    ...       ...       ...       ...
   NAV_LON_GRID_U
            Longitude                        1:1120    1:1440    ...       ...       ...       ...
   NAV_LAT_GRID_V
            Latitude                         1:1120    1:1440    ...       ...       ...       ...
   NAV_LON_GRID_V
            Longitude                        1:1120    1:1440    ...       ...       ...       ...
   NAV_LAT_GRID_W
            Latitude                         1:1120    1:1440    ...       ...       ...       ...
   NAV_LON_GRID_W
            Longitude                        1:1120    1:1440    ...       ...       ...       ...
   E3T      T-cell thickness                 1:1120    1:1440    1:51      1:0       ...       ...
   TIME_CENTERED
            Time axis                        ...       ...       ...       1:0       ...       ...
   TIME_CENTERED_BOUNDS
                                             1:2       ...       ...       1:0       ...       ...
   TIME_COUNTER
            Time axis                        ...       ...       ...       1:0       ...       ...
   TIME_COUNTER_BOUNDS
                                             1:2       ...       ...       1:0       ...       ...
   GDEPT    depth                            1:1120    1:1440    1:51      1:0       ...       ...
   M2X_RO   M2 ro   real part                1:1120    1:1440    1:51      ...       ...       ...
   M2Y_RO   M2 ro  imaaginary part           1:1120    1:1440    1:51      ...       ...       ...
   M2X_U    M2 current bcl-baro i-axis harm  1:1120    1:1440    1:51      ...       ...       ...
   M2Y_U    M2 current bcl-baro i-axis harm  1:1120    1:1440    1:51      ...       ...       ...
   M2X_V    M2 current bcl-baro j-axis harm  1:1120    1:1440    1:51      ...       ...       ...
   M2Y_V    M2 current bcl-baro j-axis harm  1:1120    1:1440    1:51      ...       ...       ...
   M2X_W    M2 current vertical harmonic re  1:1120    1:1440    1:51      ...       ...       ...
   M2Y_W    M2 current vertical imaginary p  1:1120    1:1440    1:51      ...       ...       ...
   M2X_SSH  M2 ro   real part                1:1120    1:1440    ...       ...       ...       ...
   M2Y_SSH  M2 ro  imaaginary part           1:1120    1:1440    ...       ...       ...       ...

*SHADE M2X_SSH* returns **No valid data**
(19 Oct 2016)


Possible issues:

* namelist problems in copying variables from run_nemo.scr
* inappropriate writing frequencies for harmonics in XML or namelist_cfg

Compare against::
  ``/work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00``

----


Maria output XML at 1day freq. I used 1h

Maria: namelist_cfg::

  vi namelist_cfg

  !-----------------------------------------------------------------------
  &namrun        !   parameters of the run
  !-----------------------------------------------------------------------
     nn_no       =       0   !  job number (no more used...)
     cn_exp      =  "GA"  !  experience name
     **nn_it000 = 1**
     **nn_itend = 25920**
     nn_date0 = 19820201
     nn_rstctl = 0 !  restart control = 0 nit000 is not compared to the restart file value
                   !                  = 1 use ndate0 in namelist (not the value in the restart file)
                   !                  = 2 calendar parameters read in the restart file
     nn_leapy    =       1   !  Leap year calendar (1) or not (0)
     ln_rstart   =   .true.
     cn_ocerst_in   = "restart"   !  suffix of ocean restart name (input)
     cn_ocerst_out  = "restart"   !  suffix of ocean restart name (output)
     cn_ocerst_indir   = "./"   !  directory of ocean restart name (input)
     cn_ocerst_outdir  = "./"   !  directory of ocean restart name (output)
     nn_istate   =     0     !  output the initial state (1) or not (0)
     nn_stock = 25920
     nn_write = 25920
     ...
     ...
  !-----------------------------------------------------------------------
  &nam_diaharm   !   Harmonic analysis of tidal constituents ('key_diaharm')
  !-----------------------------------------------------------------------
     **nit000_han = 1**      ! 105121  ! First time step used for harmonic analysis
     **nitend_han = 25920** ! 105120 ! 210528  ! Last time step used for harmonic analysis

Note the *han* terms are the same as the start and end of the simulation


Check the base file::

  vi EXP_harmIT/namelist_cfg

  !-----------------------------------------------------------------------
  &namrun        !   parameters of the run
  !-----------------------------------------------------------------------
  nn_no       =       0   !  job number (no more used...)
     cn_exp      =  "AMM60"  !  experience name
  **nn_it000 = 1**
  **nn_itend = 144**
  nn_date0 = 20100105
     nn_leapy    =       1   !  Leap year calendar (1) or not (0)
  ln_rstart   =   .false.
     nn_rstctl   =       2   !  restart control ==> activated only if ln_rstart=T
     cn_ocerst_in   = "restart"   !  suffix of ocean restart name (input)
     cn_ocerst_out  = "restart"   !  suffix of ocean restart name (input)
     nn_istate   =     0     !  output the initial state (1) or not (0)
  nn_stock = 1440
  nn_write = 1440
     ln_dimgnnn  = .false.   !  DIMG file format: 1 file for all processors (F) or by processor (T)
     ln_mskland  = .false.   !  mask land points in NetCDF outputs (costly: + ~15%)
     ln_clobber  = .false.   !  clobber (overwrite) an existing file
     nn_chunksz  =       0   !  chunksize (bytes) for NetCDF file (works only with iom_nf90 routines)
  /
  ...
  !-----------------------------------------------------------------------
  &nam_diaharm   !   Harmonic analysis of tidal constituents ('key_diaharm')
  !-----------------------------------------------------------------------
     **nit000_han = 1264321**          ! First time step used for harmonic analysis
     **nitend_han = 1271520**        ! Last time step used for harmonic analysis
     nstep_han  = 15        ! Time step frequency for harmonic analysis


Check the editted run file::

  vi LOGS/01271520/namelist_cfg

  !-----------------------------------------------------------------------
  &namrun        !   parameters of the run
  !-----------------------------------------------------------------------
  nn_no       =       0   !  job number (no more used...)
     cn_exp      =  "AMM60"  !  experience name
  **nn_it000 = 1264321**
  **nn_itend = 1271520**
  nn_date0 = 20100105
     nn_leapy    =       1   !  Leap year calendar (1) or not (0)
  ln_rstart   =   .true.
     nn_rstctl   =       2   !  restart control ==> activated only if ln_rstart=T
     cn_ocerst_in   = "restart"   !  suffix of ocean restart name (input)
     cn_ocerst_out  = "restart"   !  suffix of ocean restart name (input)
  nn_stock = 1271520
  nn_write = 1271520
  nn_write = 1440
     ln_dimgnnn  = .false.   !  DIMG file format: 1 file for all processors (F) or by processor (T)
     ln_mskland  = .false.   !  mask land points in NetCDF outputs (costly: + ~15%)
     ln_clobber  = .false.   !  clobber (overwrite) an existing file
     nn_chunksz  =       0   !  chunksize (bytes) for NetCDF file (works only with iom_nf90 routines)
  ...
  !-----------------------------------------------------------------------
  &nam_diaharm   !   Harmonic analysis of tidal constituents ('key_diaharm')
  !-----------------------------------------------------------------------
  **nit000_han = 1264321**
  **nitend_han = 1271520**

----

There is a problem with the output frequency of data (uneditted)::

  nn_istate   =     0     !  output the initial state (1) or not (0)
  nn_stock = 1440
  nn_write = 1440

Because the sed writing is displaced by one line overwriting nn_istate and leaving nn_write unchanged::

  nn_stock = 1271520
  nn_write = 1271520
  nn_write = 1440

Edit run_nemo sed replacement in namelist_cfg

Edit ``iodef.xml`` to output at 1day freq (like Maria)::

  vi iodef.xml
  ...
  <file_group id="1d" output_freq="1d"  output_level="10" enabled=".TRUE."> <!-- 1d files -->
    <file id="file8" name_suffix="_Tides" description="tidal harmonics" >
      <field field_ref="e3t"  />
      <field field_ref="gdept"/>
      <field field_ref="M2x_ro"       name="M2x_ro"   long_name="M2 ro   real part"                       />

Edit a 5 day simulation::

  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520

Edit submit_nemo.pbs::

  vi submit_nemo.pbs
  ...
  #PBS -l walltime=00:20:00

Resubmit::

  run_nemo
  4003527.sdb

**EXPECT hourly 3D harmonics from 5 day simulation (21 Oct 2016)**

``cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/EXP_harmIT``

| Output in WDIR with variables and axes but values all seem to be zero, or something not good. ncdump, FERRET, ncview and python didn't work
| Job run until to exceeded the wall time at 20mins: ``run_counter.txt`` not changed: ``2 1264321 1271520``
| ``less time.step: 1271519``. This is one minute from the end!


Something went wrong::
  less WDIR/stdouterr

  aprun: Apid 23790262: Caught signal Terminated, sending to application
  forrtl: error (78): process killed (SIGTERM)
  Image              PC                Routine            Line        Source
  nemo.exe           0000000001761A91  Unknown               Unknown  Unknown

----

22 Oct 2016

If there is something going wrong because of memory resource, can I scale what Maria did to get AMM7 working?

Job terminated because of wall time limit. Edit walltime::

  vi submit_nemo.pbs
  #PBS -l walltime=00:25:00

  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520

Submit::

  ./run_nemo
  4005702.sdb

**EXPECT hourly 3D harmonics from 5 day simulation (21 Oct 2016)**

``cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/EXP_harmIT``

| ``run_counter.txt`` was added to
| OUTPUT/AMM60_1d_20120601_20120605_Tides.nc 30Gb
| core dump
| run for 21mins (allowed 25)
| less time.step: 1271519

less stdouterr::

  > Error [CObjectFactory::GetObject(const StdString & id)] :
  In file '/work/n01/n01/jdha/ST/xios-1.0/src/object_factory_impl.hpp', line 79 -> [ id = 2N2x_ro, U = field ]  object is not referenced !

End of log file::

  ``tail -100 LOGS/01271520/ocean.output_EXP_harmIT``

  ...
  written ok

  anharmo_end: kt=nitend_han: Perform harmonic analysis
  ~~~~~~~~~~~~

  dia_wri_harm : Write harmonic analysis results

So, there was a problem with writing the harmonic outputs. I really don't understand where the ``2N2x_ro`` problem comes from. This variable doesn't exist.


diaharm.F90 calls iom_put for constucted variable *_ro ...::

  CALL iom_put( TRIM(tname(jh))//'x_ro', z3real(:,:,:) )
  CALL iom_put( TRIM(tname(jh))//'y_ro', z3im (:,:,:)  )
  CALL iom_put( TRIM(tname(jh))//'x_SSH', z2real(:,:) )
  CALL iom_put( TRIM(tname(jh))//'y_SSH', z2im (:,:)  )

NEED TO EDIT THE NAMELIST TO ONLY INCLUDE A FEW HARMONICS FOR ANALYSIS.
E.g Maria's namelist_cfg::

  vi /work/n01/n01/mane1/V3.6_ST/NEMOGCM/CONFIG/XIOS_AMM7_nemo/EXP00/namelist_cfg
  !-----------------------------------------------------------------------
  &nam_diaharm   !   Harmonic analysis of tidal constituents ('key_diaharm')
  !-----------------------------------------------------------------------
     nit000_han = 1      ! 105121  ! First time step used for harmonic analysis
     nitend_han = 25920 ! 105120 ! 210528  ! Last time step used for harmonic analysis
     nstep_han  = 12        ! Time step frequency for harmonic analysis
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

Edit ``/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/EXP_harmIT/namelist_cfg`` accordingly::

  vi namelist_cfg
  !-----------------------------------------------------------------------
  &nam_diaharm   !   Harmonic analysis of tidal constituents ('key_diaharm')
  !-----------------------------------------------------------------------
     nit000_han = 1264321         ! First time step used for harmonic analysis
     nitend_han = 1271520        ! Last time step used for harmonic analysis
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
Also need to add switch for 25h diagnostics (add it after the above harmonics so it does not affect ``sed`` invokation in ``run_nemo``)::

  !-----------------------------------------------------------------------
  &nam_dia25h    !   Output 25 hour mean diagnostics
  !-----------------------------------------------------------------------
      ln_dia25h   = .true.

Also added this to ``namelist_ref``::

  vi namelist_ref
  !-----------------------------------------------------------------------
  &nam_dia25h    !   Output 25 hour mean diagnostics
  !-----------------------------------------------------------------------
      ln_dia25h   = .true.

Add in 25hour diagnostics::

  vi iodef.xml
  <file_group id="1d" output_freq="1d"  output_level="10" enabled=".TRUE." > <!-- 1d files -->
    <file id="file51" name_suffix="_grid_T" description="ocean T grid variables" >
      <field field_ref="e3t"  />
      <field field_ref="gdept"/>
      <field field_ref="temper25h"    name="temper25h" long_name="sea_water_potential_temperature"    />
      <field field_ref="salin25h"     name="salin25h" long_name="sea_water_salinity"                  />
      <field field_ref="ssh25h"       name="ssh25h"   long_name="sea_surface_height_above_geoid"      />
      <field field_ref="mldr10_1"     name="mldr10_1" long_name="Mixed Layer Depth 0.01 ref.10m"      />
    </file>

    <file id="file53" name_suffix="_grid_U" description="ocean U grid variables" >
      <field field_ref="e3u"  />
      <field field_ref="gdepu"  />
      <field field_ref="ssu"          name="uos"     long_name="sea_surface_x_velocity"    />
      <field field_ref="uoce"         name="uo"      long_name="sea_water_x_velocity" />
      <field field_ref="vozocrtx25h"  name="uo25h"   long_name="sea_water_x_velocity" />
      <field field_ref="ubar"         name="ubar"    long_name="barotropic_x_velocity" />
    </file>

    <file id="file54" name_suffix="_grid_V" description="ocean V grid variables" >
      <field field_ref="e3v"  />
      <field field_ref="gdepv"  />
      <field field_ref="ssv"          name="vos"     long_name="sea_surface_y_velocity"    />
      <field field_ref="voce"         name="vo"      long_name="sea_water_y_velocity" />
      <field field_ref="vomecrty25h"  name="vo25h"   long_name="sea_water_y_velocity" />
      <field field_ref="vbar"         name="vbar"    long_name="barotropic_y_velocity" />
    </file>

    <file id="file55" name_suffix="_grid_W" description="ocean W grid variables" >
      <field field_ref="e3w"  />
      <field field_ref="gdepw"  />
      <field field_ref="vomecrtz25h"  name="wo25h"   long_name="ocean vertical velocity" />
      <field field_ref="eps25h"       name="eps25h"  long_name="TKE dissipation rate 25h " />
      <field field_ref="N2_25h"       name="N2_25h"  long_name="25h mean Brent-Viasala " />
      <field field_ref="S2_25h"       name="S2_25h"  long_name="25h squared shear" />
      <field field_ref="tke25h"       name="TKE25h"  long_name="25h vertical kinetic energy" />
    </file>

    <file id="file8" name_suffix="_Tides" description="tidal harmonics" >
      <field field_ref="e3t"  />
      <field field_ref="gdept"/>
      <field field_ref="M2x_ro"       name="M2x_ro"   long_name="M2 ro   real part"                       />
      ...


ALSO ENSURE THAT THESE ARE IN ``FIELD_DEF.XML``, IN ``/work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/SHARED``

Prepare and then resubmit::

  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1271520

  vi submit_nemo.pbs
  #PBS -l walltime=00:25:00

  ./run_nemo
  4007538.sdb


**EXPECT hourly 3D harmonics from 5 day simulation (22 Oct 2016)**
**EXPECT 25hourly outputs too**

``cd /work/n01/n01/jelt/NEMO/NEMOGCM_jdha/dev_r4621_NOC4_BDY_VERT_INTERP/NEMOGCM/CONFIG/XIOS_AMM60_nemo_harmIT/EXP_harmIT``

*NB without the 25h diagnostics (from a previous run)*::

| *30G AMM60_1d_20120601_20120605_Tides.nc*
| *19 mins of simulation for 5 days*


*For some reason there is no evidence of dia25h being initialised in the log files. It is as if dia25.F90 was not compiled...

*Note that the compile keys are different for AMM60 and Maria's AMM7 runs: https://www.evernote.com/shard/s523/nl/2147483647/9efc56c7-6965-4baf-b8be-b05f4e2b8d4c/

*`Recompile and resubmit <clean_3D_Harmonic_analysis.index>`_

-----

PLAN:

* Add in 25hr diagnostics. Check
* Do one, or two, month simulation

Edit run_counter,  to run for two months (June and July 2012 = 87,840 mins, 61 days)::

  cd EXP_harmIT
  vi run_counter.txt
  1 1 7200 20100105
  2 1264321 1352160

`Thread with a second simulation that was severed when I found this trunk simulation had problems <spare_3D_Harmoninc_analyis.html>`_

3D harmonics in AMM60 is `solved (I think...) in clean_3D_Harmonic_analysis<clean_3D_Harmonic_analysis.html>`_.
