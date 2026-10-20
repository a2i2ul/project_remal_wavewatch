# Cyclone Remal (2024): WAVEWATCH III surface-current sensitivity study

Model setup and analysis code for the preprint:

> Hakim, M. A. (2026). *Surface Current Effects on Wave Simulation During Cyclone Remal.* SSRN preprint. https://doi.org/10.2139/ssrn.7609339

Two WAVEWATCH III 6.07.1 experiments of Severe Cyclonic Storm Remal (May 2024, Bay of Bengal) share an identical grid, physics, ERA5 winds and ERA5 boundary spectra, so prescribed OSCAR surface currents are the only difference between runs (CONTROL without currents, OSCAR with them). Significant wave height from both runs, with ERA5 wave height as a benchmark, is validated against 15,635 satellite-altimeter observations from 67 passes over a common 96-hour analysis window (24 May 00 UTC to 28 May 00 UTC).

To see the results without running anything, open `Remal_WW3_main_paper_results.pdf` for the paper tables and figures, or `Remal_WW3_supplementary_robustness.pdf` for the supplementary robustness checks. Both open directly in the browser.

## Key results

- Prescribing OSCAR currents reduced pooled RMSE from 0.286 to 0.268 m (6.08%) and raised correlation from 0.945 to 0.954, but mean bias became more negative (−0.032 to −0.073 m).
- The clearest improvement came in opposing currents (RMSE difference −0.070 m at a 0.20 m s⁻¹ threshold), while strong following currents degraded performance (+0.099 m at 0.80 m s⁻¹).
- Much of the opposing-current improvement came from correcting a negative bias, and the following-current degradation came from a growing negative bias. Bias-removed error decreased overall.
- Pass-level results were mixed. OSCAR reduced mean absolute error in 30 of 66 eligible passes, and the mean pass-level difference was indistinguishable from zero.
- Prescribed currents therefore gave a modest pooled improvement concentrated in opposing currents, not a uniform benefit.

## What's in this folder

- **Remal_WW3_FINAL.ipynb**: builds the common altimeter sample, computes every table and figure in the paper plus the supplementary checks, and saves them as Word files with PDF copies
- **Remal_WW3_main_paper_results.pdf**: Tables 1, 2 and 4 and Figures 1 to 4, as produced by the notebook
- **Remal_WW3_supplementary_robustness.pdf**: supplementary Tables S1 to S4 (bias against bias-removed error, threshold consistency, bin-width sensitivity, leave-one-mission-out) and their figures
- **analysis_ready_altimeter.zip**: the collocated altimeter table (`analysis_ready_altimeter.csv`, 26,189 rows) with observed, ERA5, CONTROL and OSCAR wave heights and the current components at every observation
- **imd_hourly_track.csv**: the IMD track interpolated to hourly positions (24 May 00 UTC to 28 May 06 UTC), used for Figures 1 and 3
- **switch**: WAVEWATCH III compile switches
- **ww3_grid.inp**: grid, spectral resolution and time steps
- **ww3_prnc_wnd.inp** and **ww3_prnc_cur.inp**: ERA5 wind and OSCAR current preprocessing
- **ww3_bounc.inp**: the 398 ERA5 boundary spectra files
- **ww3_shel_control.inp** and **ww3_shel_OSC.inp**: run setup for each experiment
- **ww3_ounf_control.inp** and **ww3_ounf_OSC.inp**: NetCDF output for each experiment

Model output, forcing and grid files (depth, obstruction and mask) are not included because of their size. The WAVEWATCH III model outputs are available from the author on request (see Author). The forcing and validation datasets are public; their sources are listed in the preprint and under Data sources below.

## How to reproduce

1. **Run WAVEWATCH III.** WAVEWATCH III reads each input under its standard name, so copy the file for the experiment to that name first (for example, `ww3_shel_control.inp` becomes `ww3_shel.inp`). Run `ww3_grid`, `ww3_prnc` with `ww3_prnc_wnd.inp`, `ww3_prnc` with `ww3_prnc_cur.inp` (OSCAR only), `ww3_bounc`, `ww3_shel` and `ww3_ounf`. The two experiments differ in two places: the `Currents` line of `ww3_shel` (`F F` for CONTROL, `T F` for OSCAR) and the `CUR` output field, which only OSCAR writes.
2. **Run `Remal_WW3_FINAL.ipynb`.** Unzip `analysis_ready_altimeter.zip`, then set `PROJECT_DIR` (CELL 1) to a folder arranged like this:

   ```
   PROJECT_DIR/
   ├── Remal_WW3_FINAL.ipynb
   ├── analysis_ready_altimeter.csv          unzipped from analysis_ready_altimeter.zip
   ├── imd_hourly_track.csv                  hourly IMD track
   ├── WW3_CONTROL_WITH_NO_CURR.202405.nc    CONTROL output from ww3_ounf
   └── WW3_OSCAR_WITH_CURR.202405.nc         OSCAR output from ww3_ounf
   ```

   Then choose **Restart Kernel and Run All Cells**. Cell 3 should report 15,635 points and 67 passes. Outputs are written to `PROJECT_DIR/final_paper_outputs/`, with `figures/`, `tables/` and `figure_data/` subfolders, and the two Word files are saved there along with their PDF copies (the PDFs here). The PDF step uses LibreOffice; without it, the notebook makes a simpler PDF of the figures.

In the notebook, every value meant to be edited (paths, analysis window, figure options) is marked with `# <---------------- change this ...`.

## Where each paper figure and table comes from

**Figures** (saved in `final_paper_outputs/figures/`):

- **Figure 1**, model domain, IMD track and altimeter coverage (Section 2.1): `Figure_1_study_area`
- **Figure 2**, significant wave height against altimetry (Section 3.1): `Figure_2_Hs_validation`
- **Figure 3**, wave-height response to prescribed currents (Section 3.2): `Figure_3_WW3_current_response`, with data in `Figure_3_time_series.csv`
- **Figure 4**, current-conditioned discrepancy, response and skill (Section 3.3): `Figure_4_current_dependence`, with data in `Figure_4_threshold_curves.csv` and `Figure_4_binned_response.csv`

**Tables** (saved in `final_paper_outputs/tables/`):

- **Table 1**, configuration and experimental design: `Table_1_model_configuration.csv`
- **Table 2**, validation against the common altimeter sample: `Table_2_validation_statistics.csv`
- **Table 3**, mean bias and bias-removed error by current regime: selected rows of `Table_S1_bias_vs_centred_rmsd.csv`
- **Table 4**, consistency across satellite passes: `Table_4_pass_level_robustness.csv`

## Model configuration

- **Model:** WAVEWATCH III 6.07.1
- **Integration:** 0000 UTC 20 May to 2300 UTC 29 May 2024; analysis window 0000 UTC 24 May to 0000 UTC 28 May (96 h spin-up before it)
- **Domain:** 75–100°E, 0–25°N; 0.10° regular spherical grid (251 × 251); GEBCO 2026 bathymetry
- **Spectrum and time steps:** 32 frequencies from 0.04118 Hz (ratio 1.10), 36 directions; 600 / 300 / 300 / 150 s (global, spatial, spectral, source); hourly output
- **Physics:** ST4 source terms, NL1, BT4, DB1, RWND (current-relative wind); water levels and assimilation off
- **Forcing:** ERA5 hourly 10 m winds and ERA5 2-D boundary spectra in both runs; OSCAR L4 Final V2.0 daily 0.25° currents in the OSCAR run only

## Data sources

- **ERA5:** ECMWF ERA5 hourly single levels via the Copernicus Climate Data Store, https://doi.org/10.24381/cds.adbb2d47
- **OSCAR:** OSCAR L4 Final 0.25° V2.0 surface currents, NASA PO.DAAC, https://doi.org/10.5067/OSCAR-25F20
- **GEBCO:** GEBCO 2026 bathymetry grid, https://doi.org/10.5285/4f68d5c7-45eb-f999-e063-7086abc036fa
- **Altimetry:** Copernicus Marine Service WAVE_GLO_PHY_SWH_L3_NRT_014_001, https://data.marine.copernicus.eu/product/WAVE_GLO_PHY_SWH_L3_NRT_014_001
- **IMD:** India Meteorological Department (2024), *Severe Cyclonic Storm "REMAL" over the Bay of Bengal (24th–28th May, 2024): A report*, Cyclone Warning Division, New Delhi
- **WAVEWATCH III:** model code at https://github.com/NOAA-EMC/WW3

## Python environment

Python 3 with `numpy`, `pandas`, `xarray`, `scipy`, `matplotlib`, `cartopy` and `python-docx`, plus LibreOffice for the PDF copies.

## Citation

```bibtex
@misc{hakim2026remalww3,
  author       = {Hakim, Md. Azizul},
  title        = {Surface Current Effects on Wave Simulation During Cyclone {Remal}},
  year         = {2026},
  howpublished = {SSRN preprint},
  doi          = {10.2139/ssrn.7609339}
}
```

## Author

**Md. Azizul Hakim**, Department of Oceanography, University of Chittagong
ORCID: [0009-0000-7391-5736](https://orcid.org/0009-0000-7391-5736) · azijul.cu@gmail.com
