[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on004368-blue)](https://doi.org/10.82901/nemar.on004368)

The study was approved by the ethical committee Sud Méditérannée II (217 R01). Twenty individuals with a schizophrenia spectrum disorder (schizophrenia or schizoaffective disorder, 16 males, 4 females) and 22 healthy participants (15 males, 7 females) from the general population took part in this study. Schizophrenia and schizoaffective disorders were diagnosed based on the Structured Clinical Interview for assessing the DSM-5 criteria. The control group was screened for current or past psychiatric illness, and individuals were excluded if they met the criteria for a severe and persistent mental disorder.
We used a visual discrimination task. Stimuli consisted of 100 moving dots within a circle (3° radius) at the center of the screen. On each trial, participants indicated whether the motion direction of the dots was to the left or to the right by reaching and clicking on one of two choice targets (3° radius circle) at the top corners of the screen with a mouse. After 6 seconds without response, a buzz sound rang and a message was displayed inviting the participant to respond quicker. Motion coherence was adapted at the individual level via a 1up/2down staircase procedure in order to match task-performance between groups. Following each perceptual decision, participants were asked to report their confidence about their response using a vertical visual analog scale from 0% (Sure incorrect) to 100% (Sure correct), with 50% confidence meaning “Not sure at all”.

## NEMAR curation changes (2026-05-21, revised 2026-05-27)

The BIDS validator went from 40 errors + 1404 warnings to 0 errors + 1243 warnings. None of the raw `.set` or `.fdt` binary payloads were modified; every change is to a text sidecar.

**Event sidecar (`task-task_events.json`)**
- The `onset`, `duration`, and `response_time` columns listed `Units: "second"`; BIDS expects the SI symbol `"s"`, so the spelling was corrected. The validator was treating the spelled-out form as a redefinition of the column's required time unit.
- The `value` column originally carried a fixed list of allowed codes prefixed with the letter `x` (entries like `x12` through `x130`), but the actual cells in every `events.tsv` are bare numbers (`12`, `130`, etc.). The prefix was stripped so the keys match the data, and the source's full per-code description text was preserved verbatim in the `Levels` enumeration. One stray code (`6`, 2 occurrences, only in `sub-S37/ses-1/eeg/sub-S37_ses-1_task-task_events.tsv`) does not appear in the source's documented set; it is listed in `Levels` with a note explaining that it is undocumented in source, so the enumeration covers every value actually present in the data.

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

## NEMAR curation changes (2026-06-05): restore source Levels + add event_description column

Maintainer feedback (Arno) flagged that the numerical event codes in `events.tsv` are not interpretable. The source dataset (OpenNeuro `ds004368`) does in fact document every code that appears in the data, in the `Levels` enumeration of `task-task_events.json`'s `value` column. The previous curation pass (2026-05-21) had dropped the `Levels` enumeration to avoid one stray code falling outside it; that was a mistake. The full source documentation is restored here, and an `event_description` column is added to every `events.tsv` carrying the verbatim per-code description text so the documentation is reachable directly from each row.

**Sidecar (`task-task_events.json`) — restored and extended**
- The source's `value.Levels` enumeration was restored verbatim. The only edit was stripping the `x` prefix from each key (`"x12"` → `"12"`, etc.) so the keys match the bare-integer cells in the events.tsv data. Description text is unchanged from the source.
- The stray code `6` (2 occurrences, only in `sub-S37/ses-1/eeg/sub-S37_ses-1_task-task_events.tsv`) was added as its own `Levels` entry with a note that it is undocumented in source. This way the enumeration covers every value actually present in the data without inventing a meaning for `6`.
- A new `event_description` column was declared.

**Events tables (`*_events.tsv`, all 40 recordings)**
- A new `event_description` column was appended to every row. The cell value is a pure lookup: for each row, the value of `value` is used as the key into the sidecar's `Levels` mapping and the corresponding source-documented description string is copied into `event_description`. For value=6 the cell is `n/a` (consistent with the sidecar note that the source has no description for that code).
- 42,056 rows touched across 40 files. The four original columns (`onset`, `duration`, `sample`, `value`) are byte-identical to the pre-edit state; only the new column is appended.
- No semantic interpretation. Every `event_description` cell is either the verbatim source string or `n/a`.

Binary `.set` and `.fdt` payloads remain byte-identical.
