# HiChy: Hawaiian Chyron Dataset

HiChy is a benchmark dataset of 3,925 manually annotated television chyron (lower-third) images, paired with structured entity-extraction labels.
It was built to evaluate vision-language models on Hawaiian-language broadcast media, with a matched mainland-U.S. comparison subset.

## Citation

```bibtex
@inproceedings{lynch2026hichy,
  title     = {Structured Entity Extraction from Hawaiian Television Chyrons
               Using Vision-Language Models},
  author    = {Lynch, Kelley and King, Owen and Rim, Kyeongmin and
               Keen, Gabrielle and Chen, Yangyang and Pustejovsky, James},
  booktitle = {Proceedings of SIGUL 2026},
  year      = {2026}
}
```

## Repository contents

| Path | Description |
|---|---|
| `images/` | 3,925 chyron still images in JPEG format. |
| `golds.jsonl` | Structured annotations, one JSON object per line, one line per image. |
| `image_source_reference.csv` | Manifest mapping each image filename to its AAPB source catalog entry and an ISO 8601 timestamp into the source video. |
| `LICENSE` | CC-BY-4.0 license, covering annotations only. |
| `LICENSE-IMAGES.md` | Rights statement governing the image files. |
| `README.md` | This file. |

## Licensing and rights

This repository contains two kinds of artifacts under two different rights regimes.
Please read both license files before redistribution.

- **Annotations.**
  `golds.jsonl`, `image_source_reference.csv`, and the documentation are released under the [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/).
  The canonical legalcode is in `LICENSE`.
- **The annotation license does not apply to the images.**
  The license above applies solely to the dataset annotations.
- **Image copyright.**
  Copyright for the still images in this dataset remains with the original video copyright holders.
  Reuse of the images is subject to those rights; see `LICENSE-IMAGES.md`.
- **Source attribution.**
  Links to the archive catalog entries for the source video of each image are provided in `image_source_reference.csv`.

## Dataset statistics

| | HI (PBS Hawai`i) | Comps (mainland U.S.) |
|---|---:|---:|
| Chyrons | 1,825 | 2,100 |
| Programs | 158 | 131 |
| Avg. characters / chyron | 38.1 | 35.0 |
| Avg. words / chyron | 5.5 | 5.1 |
| % with attributes | 79.3 | 84.5 |
| % with diacritics | 0.8 | 0.0 |

### Split by subset and era

| | pre-2000 | post-2000 | total |
|---|---:|---:|---:|
| hi | 1,430 | 395 | 1,825 |
| comps | 1,679 | 421 | 2,100 |
| total | 3,109 | 816 | 3,925 |

The era boundary is set at 1999/2000 because the visual style of PBS Hawai`i chyrons changes sharply around that transition.

## Sources

HI frames are drawn from 158 PBS Hawai`i programs in the AAPB collection (1975–2008), selected to maximize coverage of Hawaiian-language names.
Comps frames are drawn from 131 mainland-U.S. public-television programs in AAPB, selected to mirror HI's typographic and graphical styles.
Candidate frames in each program were located by the [CLAMS SWT detection app](https://apps.clams.ai/swt-detection), then manually verified.

## `golds.jsonl` schema

The file contains one JSON object per line, one line per image.
Each record has the following fields:

| Field | Type | Description |
|---|---|---|
| `aapb-id` | string | AAPB GUID of the source video (e.g. `cpb-aacip-225-99n2zd03`). |
| `subset` | string | `"hi"` or `"comps"`. |
| `era` | string | `"pre-2000"` or `"post-2000"`. |
| `at` | string | ISO 8601 timestamp of the frame in the source video (`HH:MM:SS.mmm`). |
| `scene-label` | string | Scene-type label (`"I"` for chyron). |
| `scene-subtype-label` | string | Subtype label, if applicable. |
| `transitional` | bool | Whether the frame is transitional between scene types. |
| `text-transcript` | string | Verbatim transcription of on-screen chyron text. |
| `keyed-information` | object or null | Structured fields, or `null` if the annotation was not parseable. |

`keyed-information`, when present, contains:

- `name-as-written` (string)
- `name-normalized` (string)
- `attributes` (list of strings)

Example record:

```json
{
  "aapb-id": "cpb-aacip-225-99n2zd03",
  "subset": "hi",
  "era": "pre-2000",
  "at": "00:03:19.265",
  "scene-label": "I",
  "scene-subtype-label": "",
  "transitional": false,
  "text-transcript": "TOM OKAMURA (D)\n\nHouse Majority Leader",
  "keyed-information": {
    "name-as-written": "TOM OKAMURA (D)",
    "name-normalized": "Okamura, Tom",
    "attributes": ["House Majority Leader"]
  }
}
```

Each image in `images/` has filename `{aapb-id}_{program-offset-ms}_{frame-ms}_{frame-ms-aligned}.jpg`.
The `at` field is the ISO 8601 rendering of the final `{frame-ms-aligned}` value.

### Provenance

`golds.jsonl` was assembled from per-program gold annotations in the upstream annotations repository:

> https://github.com/clamsproject/aapb-annotations/tree/6a155cd/projects/understanding-chyrons/golds

The consolidation script reads the four batch raw files
(`hi-chy-{hi,comps}-{pre,post}-2000`),
looks up each record's GUID in the per-program processed gold JSONs,
adds the `aapb-id` / `subset` / `era` fields,
and emits one record per line.

## `image_source_reference.csv` schema

CSV with three columns:

| Column | Type | Description |
|---|---|---|
| `still_image` | string | Filename of the annotated chyron image. |
| `source_catalog_entry` | URL | Public AAPB catalog page for the source video. |
| `timestamp` | `HH:MM:SS.mmm` | Frame timestamp into the source video, derived from the filename. |

This file is functionally redundant with `aapb-id` / `at` in `golds.jsonl` but is kept as a standalone attribution manifest at AAPB's request.

## Annotation guidelines

Annotations were collected with the [Keystroke Labeler](https://github.com/clamsproject/aapb-annotations/tree/main/scene-recognition#tool-installation-keystroke-labeler).
Two annotators participated: a college-student primary annotator and a GBH metadata operations specialist as secondary reviewer.

### Verbatim transcription
Transcribe the text in the lower-third / chyron area of the screen verbatim, including every character.
Preserve spacing and line breaks where feasible.
Do **not** include text in the top half of the frame, watermarks, background text, or filmed text.
Easily legible logo text that is part of the chyron graphical element is included.

The `'okina` is transcribed using the backtick character (`` ` ``).

### Structured fields

For each chyron image, annotators record three structured fields:

1. **`name-as-written`.**
   The person's name copied exactly as displayed, including titles ("Dr.", "Sen.", etc.) and designations ("M.D.", "Ph.D.").
   Capitalization is preserved.
1. **`name-normalized`.**
   The same name reformatted as `Lastname, Firstname` (or `Lastname, Firstname Middlename, Suffix`).
   Capitalization is normalized.
   No characters that are absent from the verbatim form may be added.
1. **`attributes`.**
   A list of role, location, context, or other characteristic strings, one per line.
   Multi-line attributes are split only when the source chyron itself uses a hard line break to separate distinct attributes.

## Acknowledgements

This work was supported by the Andrew W. Mellon Foundation and the [American Archive of Public Broadcasting](https://americanarchive.org).
