[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on004368-blue)](https://doi.org/10.82901/nemar.on004368)

The study was approved by the ethical committee Sud Méditérannée II (217 R01). Twenty individuals with a schizophrenia spectrum disorder (schizophrenia or schizoaffective disorder, 16 males, 4 females) and 22 healthy participants (15 males, 7 females) from the general population took part in this study. Schizophrenia and schizoaffective disorders were diagnosed based on the Structured Clinical Interview for assessing the DSM-5 criteria. The control group was screened for current or past psychiatric illness, and individuals were excluded if they met the criteria for a severe and persistent mental disorder.
We used a visual discrimination task. Stimuli consisted of 100 moving dots within a circle (3° radius) at the center of the screen. On each trial, participants indicated whether the motion direction of the dots was to the left or to the right by reaching and clicking on one of two choice targets (3° radius circle) at the top corners of the screen with a mouse. After 6 seconds without response, a buzz sound rang and a message was displayed inviting the participant to respond quicker. Motion coherence was adapted at the individual level via a 1up/2down staircase procedure in order to match task-performance between groups. Following each perceptual decision, participants were asked to report their confidence about their response using a vertical visual analog scale from 0% (Sure incorrect) to 100% (Sure correct), with 50% confidence meaning “Not sure at all”.

## NEMAR curation changes (2026-05-21, revised 2026-05-27)

The BIDS validator went from 40 errors + 1404 warnings to 0 errors + 1243 warnings. None of the raw `.set` or `.fdt` binary payloads were modified; every change is to a text sidecar.

**Event sidecar (`task-task_events.json`)**
- The `onset`, `duration`, and `response_time` columns listed `Units: "second"`; BIDS expects the SI symbol `"s"`, so the spelling was corrected. The validator was treating the spelled-out form as a redefinition of the column's required time unit.
- The `value` column originally carried a fixed list of allowed codes prefixed with the letter `x` (entries like `x12` through `x130`), but the actual cells in every `events.tsv` are bare numbers (`12`, `130`, etc.). The prefix was first stripped so the keys matched the data. After that, one stray code (`6`, a single occurrence in `sub-S37/ses-1/eeg/sub-S37_ses-1_task-task_events.tsv`) still fell outside the documented set, meaning the source has at least one event code it does not document. Rather than guess at a meaning for `6`, the strict allowed-values list was dropped entirely so the column is free-form numeric. The documented codes (12: stimulus onset, 13: first mouse-move for the perceptual task, and so on) are preserved in the column's `Description` text so the semantic mapping is not lost.

**Channel tables (`channels.tsv`, all 40 recordings)**
- The `type` column was `n/a` for every row; it was set to `EEG` for all 63 rows in each file. The recordings are scalp EEG (the per-recording `_eeg.json` declares `Manufacturer: "g.tec"` and `EEGChannelCount: 63`), and the validator's channel-count check compares declared `EEGChannelCount` against the count of `type=EEG` rows. Leaving the type blank made the validator see zero EEG rows and flag a count mismatch for every recording.
- The `units` column was also `n/a`; it was set to `uV` for all 63 rows. Microvolts are the canonical unit for scalp EEG and declaring it removes the per-row missing-unit warning. The channel-name column was not touched.

**Dataset description (`dataset_description.json`)**
- Added `DatasetType: "raw"` so the dataset is validated as raw data rather than a derivative.
- Updated `BIDSVersion` from `1.1.1` to `1.11.1` (the version the current validator checks against).
- `GeneratedBy` was left absent, exactly as the source published it — nothing was added there.
- The `ReferencesAndLinks` field was `[""]` (a list containing an empty string, which is not a valid reference URL); it was set to `[]`.

**Remaining warnings (1243) — left on purpose**
- These are all "recommended but missing" fields that need information from the study, lab, or equipment that is not in the dataset (for example: manufacturer model name and serial number, software versions, institution name and address, head circumference, hardware filters, cognitive-atlas IDs, stimulus-presentation software, HED schema version). `GeneratedBy` on `dataset_description.json` is among these recommended-but-missing fields and is left blank rather than filled with a synthetic provenance entry. One additional warning flags an events table whose onsets are not strictly increasing; that reflects the source data and was not rewritten.
