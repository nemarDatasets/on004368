[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on004368-blue)](https://doi.org/10.82901/nemar.on004368)

The study was approved by the ethical committee Sud Méditérannée II (217 R01). Twenty individuals with a schizophrenia spectrum disorder (schizophrenia or schizoaffective disorder, 16 males, 4 females) and 22 healthy participants (15 males, 7 females) from the general population took part in this study. Schizophrenia and schizoaffective disorders were diagnosed based on the Structured Clinical Interview for assessing the DSM-5 criteria. The control group was screened for current or past psychiatric illness, and individuals were excluded if they met the criteria for a severe and persistent mental disorder.
We used a visual discrimination task. Stimuli consisted of 100 moving dots within a circle (3° radius) at the center of the screen. On each trial, participants indicated whether the motion direction of the dots was to the left or to the right by reaching and clicking on one of two choice targets (3° radius circle) at the top corners of the screen with a mouse. After 6 seconds without response, a buzz sound rang and a message was displayed inviting the participant to respond quicker. Motion coherence was adapted at the individual level via a 1up/2down staircase procedure in order to match task-performance between groups. Following each perceptual decision, participants were asked to report their confidence about their response using a vertical visual analog scale from 0% (Sure incorrect) to 100% (Sure correct), with 50% confidence meaning “Not sure at all”.

## NEMAR curation changes (2026-05-21)

BIDS validator: 40 errors + 1404 warnings → 0 errors + 1242 warnings. Raw `.set`/`.fdt` binary payloads unchanged.

### `dataset_description.json`
- Added `DatasetType: "raw"`. Why: BIDS-validator otherwise infers a derivative-rules cascade when `DatasetType` is missing alongside `GeneratedBy`, producing spurious warnings.
- Added `GeneratedBy: [{Name: "nemar-cli", Version: "0.8.8", CodeURL: "https://github.com/nemar-org/nemar-cli"}]`. Why: records the NEMAR rehost step in the dataset's provenance chain.
- Bumped `BIDSVersion` `1.1.1` → `1.8.0`. Why: the previous value is below the validator's recognised-version floor.
- `ReferencesAndLinks`: `[""]` → `[]`. Why: an empty-string element is not a valid reference URL.

### `task-task_events.json`
- `onset`, `duration`, `response_time` columns: `Units: "second"` → `Units: "s"`. Why: BIDS-canonical SI symbol is `"s"`; the previous wording fires `TSV_COLUMN_TYPE_REDEFINED`.
- `value` column: dropped the 36-entry `Levels` enum (keys were `x12`–`x17`, `x101`–`x130` with an `x` prefix that did not match the bare-numeric cells in every `events.tsv`). Closed the 40× `TSV_VALUE_INCORRECT_TYPE:value` errors on line 2 of each recording. After stripping the prefix, one additional value (`6`, single occurrence in `sub-S37/ses-1/eeg/sub-S37_ses-1_task-task_events.tsv` line 264) still fell outside the documented set, so the enum was dropped entirely and the column declared as free-form numeric. The original Levels content is summarised in the column `Description` so the semantic mapping for the documented codes (12: stimulus onset, 13: first mouse-move for perceptual task, …) is not lost.

### `sub-S*/ses-*/eeg/sub-S*_ses-*_task-task_channels.tsv` (40 files)
- `type` column: `n/a` → `EEG` for all 63 rows in every file. Why: the validator's `EEG_CHANNEL_COUNT_MISMATCH` rule compares the count of `type=EEG` rows against the `EEGChannelCount` declared in the per-recording `_eeg.json` (`63`); leaving the column at `n/a` made the validator see zero EEG rows and fire the warning 80 times (one per recording per check). Channel-name column unchanged.
- `units` column: `n/a` → `uV` for all 63 rows. Why: the source dataset processed scalp EEG through standard preprocessing (per-recording `_eeg.json` declares `Manufacturer: "g.tec"`), and microvolt is the canonical unit for scalp EEG; declaring it avoids a unit-missing warning per row.
