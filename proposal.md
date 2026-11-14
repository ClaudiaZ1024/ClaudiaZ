# Proposal: Position of the amide I band in SRS spectra of human skin

Claudia Zhang, CHEM 527

## 1. Question

In hyperspectral SRS spectra of a human skin section, at what Raman shift (cm⁻¹) is the maximum of the amide I band, and does it agree with the literature value for skin collagen (about 1661 cm⁻¹)?

## 2. Data source

- **Dataset:** Sorrentino, S. et al. (2026). SRS_Spontaneous-Raman-Comparison_Dataset (v1.0) [Data set]. Zenodo. https://doi.org/10.5281/zenodo.22211391
- **License:** CC BY 4.0, as stated on the Zenodo record (the README inside the archive leaves the license as an unfilled placeholder and refers to the repository)
- **Accessed:** 2026-11-13
- **Associated paper:** the Zenodo record names the paper "Band-dependent fidelity of hyperspectral stimulated Raman scattering microscopy: a co-registered pixel-wise benchmark against confocal spontaneous Raman microscopy" but gives no journal or DOI, and I could not find a published version as of the access date. I therefore cite the dataset itself.
- **Files I use**, committed unmodified to `data/skin_section_2/`:
  - `skin_srs_small_sampling_hyperspectral_registered_confocal.pickle` (45 MB): the SRS hyperspectral cube
  - `srs_Raman_shift.pickle` (1 KB): the Raman shift of each spectral channel
- The dataset also contains spontaneous Raman spectra of the same section, but that file is 748.8 MB, over GitHub's 100 MB limit, so I do not use it.

## 3. Is the data sufficient?

**Observations.** The cube is 300 × 300 pixels × 125 spectral channels. One observation is one pixel, which has one SRS spectrum, so there are 90,000 spectra.

**Variables.**

| Variable | Unit | Range |
|---|---|---|
| Raman shift | cm⁻¹ | 708.7 to 1750, 125 channels, spacing 7.6 to 9.2 |
| SRS intensity | arbitrary units (float32) | 0 to 246.9 |
| Pixel position | pixel index | 300 × 300 |

**Why this is enough to answer the question.** In the mean spectrum of all 90,000 pixels the amide I band is the strongest feature, with its highest sampled point at 1658.4 cm⁻¹ and about ten channels across the band. The spectral range extends about 90 cm⁻¹ above it, so there are channels on both sides for a baseline. With 90,000 pixels I can average to raise the signal-to-noise ratio and resample pixels to estimate the uncertainty of the fitted position.

**Planned methods.**

1. First analysis (working draft): sort the Raman shift axis, which is stored in descending order, and compute the mean spectrum.
2. Second method: fit a peak model plus a linear baseline to the amide I band with `curve_fit`, and report the peak position in cm⁻¹ with its uncertainty.
3. Named comparison: the literature position of amide I in skin collagen, 1661 cm⁻¹ (Molecules 2019, 24(11), 2059, Table 1; doi:10.3390/molecules24112059). As an extra check, a fixed-seed Poisson noise simulation on the same 125-channel axis, with a peak at a known position, shows how far the fitted position is biased at single-pixel signal-to-noise.

**What is missing.**

- The channel spacing is about 8 cm⁻¹. This limits how precisely the position can be found and prevents resolving the 1654 and 1661 cm⁻¹ components.
- The band has a shoulder on its low-wavenumber side, so a single symmetric peak may not describe it; the fitted position depends on the peak model I choose.
- The intensity is in arbitrary units, so I cannot report concentrations, only positions and relative intensities.
- The cube is processed, not raw. The README says the registered files "should not be interpreted as independent raw acquisitions" and lists calibration, baseline correction, registration and interpolation among the processing steps, without giving the parameters. I cannot undo this, and the noise in the cube is therefore not simple counting noise; the Poisson simulation tests my fitting method, not the noise of this dataset.
- Without the spontaneous Raman file, I have no second technique on the same sample; the comparison is to literature values from other skin samples.
- One section from one tissue type; the result does not generalize to other tissues.
- The folder for this section holds one SRS acquisition, so there are no repeat measurements; my uncertainty comes from the fit and from resampling pixels, not from repeated experiments.
- Neither the Zenodo record nor the README states the physical pixel size, so I refer to pixels by index. The question is about a spectral position, so this does not affect the answer.
- The README points to the manuscript and its code repository for acquisition settings and processing parameters, but neither is linked or available yet.

**If the data cannot answer the question.** If the amide I band cannot be fitted reliably in single-pixel or mean spectra, I will answer a smaller question on the same data: the position of the CH₂ scissoring band near 1450 cm⁻¹ in region-averaged spectra, compared with its literature value.
