# Validation of EOlife X — data and interactive review tool

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.21759367.svg)](https://doi.org/10.5281/zenodo.21759367)

![Graphical abstract. EOlife X compared against reference measurements in a porcine
cardiac arrest model, n = 11. Regular ventilation, 581 breaths: bias and limits of
agreement within the clinically acceptable difference for inspiratory tidal volume,
expiratory tidal volume and ventilation rate. Asynchronous intra-arrest ventilation,
240 breaths: inspiratory tidal volume bias −26 mL but limits of agreement −355 to
+304 mL; expiratory tidal volume bias −281 mL, limits −599 to +37 mL; ventilation rate
bias +23.8 per minute, limits −50.2 to +97.8. Reverse airflow episodes are a possible
mechanism.](graphical-abstract.svg)

<sub>Graphical abstract designed by [David Purkarthofer](https://github.com/dpurkarthofer).</sub>

Data and an interactive review interface accompanying:

> Orlob S, Purkarthofer D, Kern WJ, Hackl B, Dunz J, Eder S, Bartz F, Fruhwald S,
> Wnent J, Gräsner JT, Holler M, Martini J, Putzer G.
> **Measurement of tidal volumes and respiratory rate during regular and asynchronous
> intra-arrest ventilation using the EOlife X device: A method-comparison study in a
> porcine cardiac arrest model.**
> *Resuscitation*, 2026.
> [doi:10.1016/j.resuscitation.2026.111217](https://doi.org/10.1016/j.resuscitation.2026.111217)

## The study in brief

EOlife X is a ventilation-feedback device that reports inspiratory and expiratory tidal
volume and ventilation rate. We compared its readings against reference flow and pressure
measurements in a **porcine cardiac arrest model (n = 11)**, under two conditions:

- **regular ventilation** — 581 ventilations
- **asynchronous intra-arrest ventilation** — 240 ventilations, delivered continuously
  during ongoing chest compressions via an endotracheal tube

During regular ventilation, bias and limits of agreement were within the clinically
acceptable difference for all three parameters. During asynchronous intra-arrest
ventilation, agreement degraded: inspiratory tidal volume kept an acceptable bias
(−26 mL) but unacceptably wide limits of agreement (−355 to +304 mL), while expiratory
tidal volume (bias −281 mL) and ventilation rate (bias +23.8 min⁻¹) fell outside the
acceptable difference in both bias and limits. Reverse airflow episodes during chest
compressions are a possible mechanism.

Full methods and results are in the article; this repository holds the underlying
records so the analysis can be inspected breath by breath.

## How the data were produced

The recordings come from experiments conducted between **November 2022 and January 2023**
at the experimental anaesthesia laboratory of the Medical University of Innsbruck, one
animal per experiment day — which is why each file is named by its date. The ancillary
measurements under `data_publication-II/` were carried out separately, in **May 2026**.

EOlife X was integrated into the ventilation circuit of **15 animals**. Four were
excluded: two for deviation from the ventilation protocol and two for recording failure.
The **11 cases** published here are the remainder.

**The ground truth comes from a separate publication.** The breath segmentation and the
per-breath reference values in these files rest on the flow–pressure product method,
published as *Multiplying flow and pressure: detecting respiratory phases in intra-arrest
ventilation*
([doi:10.1016/j.resuscitation.2026.111050](https://doi.org/10.1016/j.resuscitation.2026.111050)).
That work is the foundation of the reference against which EOlife X is compared here, not
a side result. Its own data publication covers 13 cases from the same series; the two
datasets share animals and dates but no files, each having been processed for its own
analysis.

The same experiments also supported a study with an entirely different focus, on the
neurochemistry of cardiac arrest: *The Neurochemical Signature of Cardiac Arrest: A
Multianalyte Online Microdialysis Study*, ACS Chemical Neuroscience, 2025
([doi:10.1021/acschemneuro.4c00777](https://doi.org/10.1021/acschemneuro.4c00777)).

**Ethical approval.** The Austrian Ministry of Education, Science and Research authorised
the experiments (BMBWF-V/3b GZ:2021-0.895.386). The ancillary measurements were
authorised separately (BMBWF-V/3b GZ:2023-0.288.343). The experiments complied with
Austrian and European regulations concerning the use of laboratory animals.

**Reference measurements.** Airway flow was recorded with a purpose-built volumetric
capnograph, described in Kern WJ, *Towards a data-driven cardiac arrest treatment*, PhD
thesis, University of Graz, 2024
([urn:nbn:at:at-ubg:1-207085](https://unipub.uni-graz.at/urn/urn:nbn:at:at-ubg:1-207085)).
The four reference channels in each file are these signals interpolated onto a common
time base.

**EOlife X measurements.** The device cannot record internally. A webcam therefore filmed
its display alongside the ventilator (Evita XL) and a clock, and the displayed values
were extracted from the video frame by frame by optical character recognition, then
manually validated against the corresponding frames. A reading was kept only where every
value could be extracted and no implausibility was flagged. This is why the three
EOlife X channels are far sparser than the reference channels — roughly 20,000 samples
against some 490,000 in a typical case — and why they are step-wise rather than
continuous: they are what the clinician would have seen on the screen, not an internal
data stream.

The two sources were synchronised on the first respiratory cycle of each measurement
period.

## What is here

```
data/                        11 cases, one JSON per experiment day
review_data.ipynb            interactive review interface (main publication)
environment.yml              conda environment for Binder / local use
voila.json                   Voilà configuration
data_publication-II/         ancillary experiment (same study, see below)
├── data/aux_X0001126.json
├── review_aux.ipynb
└── review_sensitivity.ipynb
```

Despite its name, `data_publication-II/` is **not** a second publication. It holds an
ancillary experiment conducted for the study above, with its recording, a review
notebook, and the notebook for the sensitivity analysis.

## Data format

Each file in `data/` is one experiment day, named by date (`YYMMDD`), which is also the
`case_index` in its metadata. Files are serialised
[`vitabel`](https://github.com/UniGrazMath/vitabel) `Vitals` objects (written with
vitabel 0.1.1) and hold time-series **channels** plus annotation **labels**.

**Channels** — the four reference signals are interpolated onto a common high-rate time
base; the three EOlife X series are the values read from the device display.

| Channel | Source | Meaning |
|---|---|---|
| `Flow Interpolated` | reference | airway flow |
| `Pressure Interpolated` | reference | airway pressure |
| `Inspiratory Volume` | reference | inspiratory volume, *V*<sub>i</sub> |
| `Expiratory Volume` | reference | expiratory volume, *V*<sub>e</sub> |
| `vi` | EOlife X | inspiratory tidal volume shown by the device |
| `ve` | EOlife X | expiratory tidal volume shown by the device |
| `f` | EOlife X | ventilation rate shown by the device |

**Labels** — breath segmentation (`Inspiration Begin`, `Expiration Begin`, `Inspiration`,
`Expiration`, `Respiratory Rate`), experimental phase (`baseline`, `cpr`), reference
values per breath (`insp_gt`, `exp_gt`, `f_gt`, `rr_corr_gt`), the device values paired
to each breath (`VTinsp`, `VTexp`), and the variants used for the sensitivity analysis
(`VTinsp_sens`, `VTexp_sens`).

## Running the review interface

Each notebook renders as a Voilà app: pick a case from the dropdown to see the reference
signals and annotations in synchronised subplots, with a clickable overview of the full
recording beneath for navigation.

**In your browser, nothing to install:**

| | |
|---|---|
| [![Launch the review interface](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/zenodo/10.5281/zenodo.21759367/?urlpath=%2Fvoila%2Frender%2Freview_data.ipynb) | **Review** — the eleven main cases |
| [![Launch the ancillary review](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/zenodo/10.5281/zenodo.21759367/?urlpath=%2Fvoila%2Frender%2Fdata_publication-II%2Freview_aux.ipynb) | **Ancillary experiment** — the single-subject recording |
| [![Launch the sensitivity review](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/zenodo/10.5281/zenodo.21759367/?urlpath=%2Fvoila%2Frender%2Fdata_publication-II%2Freview_sensitivity.ipynb) | **Sensitivity analysis** — breath pairing, on the main cases |

These run on [Binder](https://mybinder.org/) from the archived Zenodo release rather than
from this branch, so they show released data whatever happens here later. The first start
takes a few minutes while the environment is built, and Voilà then executes the notebook
before the interface appears — allow about a minute more. To read or modify the code
instead, replace `voila/render` in the URL with `doc/tree`.

Locally:

```bash
conda env create -f environment.yml
conda activate binder-environment
voila review_data.ipynb
```

The environment pins `vitabel==0.1.1`, the version the files were written with.

## Citing

If you use these data, please cite the article above.

The dataset itself is archived on Zenodo and carries its own DOI:

- **All versions:** [10.5281/zenodo.21759367](https://doi.org/10.5281/zenodo.21759367) —
  use this one unless you need to pin an exact snapshot
- **v1.0.0:** [10.5281/zenodo.21759368](https://doi.org/10.5281/zenodo.21759368)

## Acknowledgements

[David Purkarthofer](https://github.com/dpurkarthofer) designed the graphical abstract
and is a co-author of the study.

## Licence

MIT — see [LICENSE](LICENSE).

The graphical abstract (`graphical-abstract.svg`) is reproduced
from the article, which is published open access under
[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/), and is covered by that licence
rather than by the MIT licence above.
