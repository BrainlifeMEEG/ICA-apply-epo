<h1 align="center">Reject ICA components</h1>

<p align="center">
  Removes ICA components from epoched MEG/EEG data with MNE-Python ICA.apply, using manually listed and/or automatically detected EOG and ECG components, with an overlay figure and HTML report.
</p>

<p align="center">
  <a href="https://github.com/brainlife/abcd-spec"><img src="https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg" alt="ABCD Spec v1.1"></a>
  <a href="https://doi.org/10.25663/brainlife.app.764"><img src="https://img.shields.io/badge/Brainlife-brainlife.app.764-blue.svg" alt="Run on brainlife.io"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-see_LICENSE-lightgrey.svg" alt="License"></a>
</p>

## At a glance

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **Removes ICA components from epoched MEG/EEG data with MNE-Python ICA.apply, using manually listed and/or automatically detected EOG and ECG components, with an overlay figure and HTML report.**

Reads epochs and a fitted ICA solution, builds the exclusion list from the components already marked in the ICA, the indices in exclude and, optionally, components detected by find_bads_eog and find_bads_ecg, then reconstructs the epochs without them. Use it after fitting ICA to clean ocular and cardiac artifacts. The optional rejects input is not read by the code.

| | |
|---|---|
| **Inputs** | `neuro/meeg/mne/epochs` · `neuro/meeg/mne/ica` · `neuro/meg/fif-override` (optional) |
| **Outputs** | `neuro/meeg/mne/epochs` · `generic/image/png` · `report/html` |
| **Container** | `brainlifemeeg/mne:1.12.1` |
| **Resources** | 8 CPUs · 16 GB memory · 0.5 h walltime |
| **Run it** | [doi.org/10.25663/brainlife.app.764](https://doi.org/10.25663/brainlife.app.764) |

#### Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `epo` | `neuro/meeg/mne/epochs` | yes | Epoched MEG/EEG data (-epo.fif). |
| `ica` | `neuro/meeg/mne/ica` | yes | Fitted ICA decomposition of the same data (e.g. from an ICA-fit App). |
| `rejects` | `neuro/meg/fif-override` | no | We can use a single comma-separated line of component indices to exclude . Not read by the current code. |

#### Outputs

| Output | Datatype | Description |
|---|---|---|
| `out_dir` | `neuro/meeg/mne/epochs` | The corrected data. |
| `out_figs` | `generic/image/png` | plot_overlay.png comparing the data before and after removing the components. |
| `out_report` | `report/html` | HTML QC report with the ICA decomposition and the excluded components. |

<details>
<summary><b>Parameters</b> (5)</summary>

| Parameter | Type | Default | Description |
|---|---|---|---|
| `exclude` | string |  | The indices referring to columns in the ummixing matrix. The components to be zeroed out. If None (default) or an empty list, only components from ica.exclude will be excluded. Else, the union of exclude and ica.exclude will be excluded. |
| `reject_EOG` | boolean | `false` | Detect and reject EOG related components using correlation (using mne.preprocessing.ICA.find_bads_eog) |
| `reject_ECG` | boolean | `false` | Detect and reject ECG related components using correlation (using mne.preprocessing.ICA.find_bads_ecg) |
| `EOG_chan` | string |  | Comma separated EOG channel number(s) or label(s) |
| `ECG_chan` | string |  | Comma separated ECG channel number(s) or label(s) |

</details>

<!-- brainlife-app:end -->

## Running the App

### On brainlife.io

Open the App at [doi.org/10.25663/brainlife.app.764](https://doi.org/10.25663/brainlife.app.764), choose `neuro/meeg/mne/epochs`, `neuro/meeg/mne/ica`, `neuro/meg/fif-override` from your project, set the parameters and press **Execute**. The results are saved to your project as `neuro/meeg/mne/epochs`, `generic/image/png`, `report/html`.

### Locally

You need [Singularity/Apptainer](https://apptainer.org/) (the App runs in `brainlifemeeg/mne:1.12.1`), [`jq`](https://jqlang.github.io/jq/).

1. Clone this repository.
2. Create `config.json` with the paths to your inputs and the parameters:

   ```json
   {
       "epo": "/path/to/epo/epo",
       "ica": "/path/to/ica/ica",
       "channels": "/path/to/rejects/channels",
       "headshape": "/path/to/rejects/headshape",
       "destination": "/path/to/rejects/destination",
       "events": "/path/to/rejects/events",
       "events_json": "/path/to/rejects/events_json",
       "info": "/path/to/rejects/info",
       "exclude": "",
       "reject_EOG": false,
       "reject_ECG": false,
       "EOG_chan": "",
       "ECG_chan": ""
   }
   ```

3. Run `./main`.

## Configuration Parameters

| key | type | default | description |
|---|---|---|---|
| `exclude` | string | `""` | Comma-separated list of component indices to exclude, in addition to any already in `ica.exclude`. The union of the two is excluded. |
| `reject_EOG` | boolean | `false` | Whether to automatically detect and exclude EOG artifact components using `mne.preprocessing.ICA.find_bads_eog`. |
| `EOG_chan` | string | `""` | EOG channel name or index for automatic EOG detection (optional). |
| `reject_ECG` | boolean | `false` | Whether to automatically detect and exclude ECG artifact components using `mne.preprocessing.ICA.find_bads_ecg`. |
| `ECG_chan` | string | `""` | ECG channel name or index for automatic ECG detection (optional). |

## Local Testing

```bash
# Edit config.json to point "epo" and "ica" at real files, then:
python main.py
```

## Technical Details

The app uses MNE-Python's ICA functionality to apply component exclusion to epoched data. It supports:
- Manual component exclusion via index specification
- Automatic EOG artifact detection using correlation analysis
- Automatic ECG artifact detection using cross-trial phase statistics
- Quality control visualization and reporting

## Citing

If you use this App, please cite it, the software it builds on and brainlife.io:

- Reject ICA components. brainlife.io App. [doi:10.25663/brainlife.app.764](https://doi.org/10.25663/brainlife.app.764)
- Gramfort A. et al. MEG and EEG data analysis with MNE-Python. Frontiers in Neuroscience 7:267 (2013). [doi:10.3389/fnins.2013.00267](https://doi.org/10.3389/fnins.2013.00267)
- Hayashi S, Caron BA, Heinsfeld AS, et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. *Nature Methods* 21:809–813 (2024). [doi:10.1038/s41592-024-02237-2](https://doi.org/10.1038/s41592-024-02237-2)
- Avesani P, McPherson B, Hayashi S, et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. *Scientific Data* 6:69 (2019). [doi:10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)

## Authors

- Saeed Zahran (https://github.com/saeedzahranutc)
- Maximilien Chaumon (https://github.com/dnacombo)

## Funding

[![Wellcome-226486/Z/22/Z](https://img.shields.io/badge/Wellcome-226486%2FZ%2F22%2FZ-purple.svg)](https://wellcome.org/)
[![NIH-NINDS-U24NS140384](https://img.shields.io/badge/NIH_NINDS-U24NS140384-green.svg)](https://reporter.nih.gov/project-details/11261037)
[![NIH-NINDS-UM1NS132207](https://img.shields.io/badge/NIH_NINDS-UM1NS132207-green.svg)](https://reporter.nih.gov/project-details/11411328)
[![NIH-NIBIB-R01EB029272](https://img.shields.io/badge/NIH_NIBIB-R01EB029272-green.svg)](https://reporter.nih.gov/project-details/10262925)
[![NIH-NIBIB-R01EB030896](https://img.shields.io/badge/NIH_NIBIB-R01EB030896-green.svg)](https://reporter.nih.gov/project-details/10428625)
[![NIH-NIMH-R01MH126699](https://img.shields.io/badge/NIH_NIMH-R01MH126699-green.svg)](https://reporter.nih.gov/project-details/10460628)
[![NIH-NIDCD-R01DC020088](https://img.shields.io/badge/NIH_NIDCD-R01DC020088-green.svg)](https://reporter.nih.gov/project-details/11140284)
[![NSF-BCS-1734853](https://img.shields.io/badge/NSF_BCS-1734853-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1734853)
[![NSF-BCS-2203524](https://img.shields.io/badge/NSF_BCS-2203524-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2203524)
[![NSF-IIS-1636893](https://img.shields.io/badge/NSF_IIS-1636893-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1636893)
[![NSF-OAC-2148729](https://img.shields.io/badge/NSF_OAC-2148729-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2148729)
[![NSF-IIS-1912270](https://img.shields.io/badge/NSF_IIS-1912270-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1912270)
[![NSF-IIS-2148700](https://img.shields.io/badge/NSF_IIS-2148700-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=2148700)
[![NSF-OAC-1916518](https://img.shields.io/badge/NSF_OAC-1916518-blue.svg)](https://www.nsf.gov/awardsearch/showAward?AWD_ID=1916518)

<sub>brainlife.io, which runs this App, is publicly funded by Wellcome, the U.S. National Institutes of Health and the U.S. National Science Foundation; acknowledging that support helps keep it running.</sub>

## License

See [LICENSE](LICENSE).
