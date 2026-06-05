[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on004368-blue)](https://doi.org/10.82901/nemar.on004368)

The study was approved by the ethical committee Sud Méditérannée II (217 R01). Twenty individuals with a schizophrenia spectrum disorder (schizophrenia or schizoaffective disorder, 16 males, 4 females) and 22 healthy participants (15 males, 7 females) from the general population took part in this study. Schizophrenia and schizoaffective disorders were diagnosed based on the Structured Clinical Interview for assessing the DSM-5 criteria. The control group was screened for current or past psychiatric illness, and individuals were excluded if they met the criteria for a severe and persistent mental disorder.
We used a visual discrimination task. Stimuli consisted of 100 moving dots within a circle (3° radius) at the center of the screen. On each trial, participants indicated whether the motion direction of the dots was to the left or to the right by reaching and clicking on one of two choice targets (3° radius circle) at the top corners of the screen with a mouse. After 6 seconds without response, a buzz sound rang and a message was displayed inviting the participant to respond quicker. Motion coherence was adapted at the individual level via a 1up/2down staircase procedure in order to match task-performance between groups. Following each perceptual decision, participants were asked to report their confidence about their response using a vertical visual analog scale from 0% (Sure incorrect) to 100% (Sure correct), with 50% confidence meaning “Not sure at all”.

## NEMAR curation changes

Changes applied to the OpenNeuro source during the NEMAR rehost. The BIDS validator goes from 40 errors + 1404 warnings to 0 errors + 1243 warnings; the residual warnings are recommended-but-missing study-specific fields that cannot be filled without information from the original lab. None of the raw `.set` or `.fdt` binary payloads are modified; every change is to a text sidecar.

**Dataset description (`dataset_description.json`)**
- `DatasetType: "raw"` is added so the dataset is validated as raw data rather than a derivative.
- `BIDSVersion` is set to `1.11.1`, the version the current validator checks against.
- `GeneratedBy` is absent, matching the source. Nothing is added there.
- `ReferencesAndLinks` was `[""]` (a list containing an empty string, not a valid reference URL); it is now `[]`.

**Event sidecar (`task-task_events.json`)**
- `Units` on the `onset`, `duration`, and `response_time` columns is `"s"` (the BIDS-required SI symbol). The source spelled it as `"second"`, which the validator treated as a redefinition of the column's required time unit.
- `value.Levels` carries the source's documented mapping verbatim. The source-published keys were prefixed (`x12` … `x130`) but every actual cell in `events.tsv` is a bare integer; the keys are unprefixed here (`12` … `130`) so they match the data. The description text on each level is unchanged from the source: codes 12–17 cover the per-trial events (stimulus onset, mouse-move and response for the perceptual task, onset of the confidence task, mouse-move and judgment for the confidence task), and codes 101–130 all carry the description "Trial onset".
- The integer `6` (2 occurrences, only in `sub-S37/ses-1/eeg/sub-S37_ses-1_task-task_events.tsv`) does not appear in the source's documented set. It is listed in `Levels` with a note that it is undocumented in source, so the enumeration covers every value actually present in the data without inventing a meaning for it.
- A new `event_description` column is declared (see next bullet group).

**Events tables (`*_events.tsv`, all 40 recordings)**
- An `event_description` column is appended to every row. The cell value is a pure lookup: the row's `value` is used as the key into the sidecar's `Levels` mapping and the source-documented description string is copied verbatim into `event_description`. For `value=6` the cell is `n/a` (matching the sidecar note that the source has no description for that code).
- 42,056 rows touched across 40 files. The four original columns (`onset`, `duration`, `sample`, `value`) are byte-identical to the source; only the new column is appended.

**Channel tables (`channels.tsv`, all 40 recordings)**
- The `type` column is `EEG` for all 63 rows in each file. The source had `type=n/a` for every row, which made the validator's channel-count check see zero EEG channels and flag a count mismatch against the sidecar's `EEGChannelCount: 63`. The recordings are scalp EEG (the per-recording `_eeg.json` declares `Manufacturer: "g.tec"`).
- The `units` column is `uV` for all 63 rows. The source had `units=n/a`; microvolts are the canonical unit for scalp EEG and declaring it removes the per-row missing-unit warning. The channel-name column is not touched.

**Remaining warnings (1243)**
- These are all "recommended but missing" fields that need information from the study, lab, or equipment that is not in the dataset (for example: manufacturer model name and serial number, software versions, institution name and address, head circumference, hardware filters, cognitive-atlas IDs, stimulus-presentation software, HED schema version). `GeneratedBy` on `dataset_description.json` is among these recommended-but-missing fields and is left blank rather than filled with a synthetic provenance entry. One additional warning flags an events table whose onsets are not strictly increasing; that reflects the source data and is not rewritten.
