# Local Bible texts

This directory contains Bible verse text bundled with the website and served directly by its local `Bible` adapters. These files are used as selectable translations on the Bible Study page; they are not the Strong's-tagging or lexicon data.

## Contents

- `statenvertaling/`: Dutch Statenvertaling verse text, one compressed JSON file per Bible book. The `LocalStatenvertalingBible` adapter in `walkasjesus_app/models/bibles.py` registers it as `NL - SV - Statenvertaling` (`svv-statenvertaling`).

The app reads these files locally, so displaying the Statenvertaling does not require an API.Bible request and does not consume API quota. The source is CrossWire's public-domain `DutSVVA` SWORD module.

## File format

Each `<Book>.json.gz` is UTF-8 JSON with this structure:

```json
{
  "book": "Genesis",
  "source": "Statenvertaling met Strong-nummers",
  "chapters": {
    "1": {
      "1": "In den beginne schiep God den hemel en de aarde."
    }
  }
}
```

Chapter and verse keys are strings. Text has OSIS markup, notes and headings removed. Only canonical books supported by the app are exported.

## Relationship to tagged Bible data

The matching Strong's tags and Dutch gloss/concordance data are in the separate `data/tagged_bibles/svv/` submodule. The datasets are generated from the same CrossWire module so their verse text and word alignments correspond. In short:

- `data/local_bibles/statenvertaling/` provides the readable verse text.
- `data/tagged_bibles/svv/` provides word-level Strong's links, Dutch word renderings and occurrence references.

The Bible Study Strong's panel uses both: the selected Bible shows its verse text, while the tagged data links each Hebrew/Greek token to its Statenvertaling wording and usage counts.

## Regenerating

From `walkasjesus_website/`, install the optional generation dependency and run:

```bash
pip install pysword
python manage.py build_tagged_strongs_index svv
```

The command downloads CrossWire's `DutSVVA` module and writes both the tagged submodule data and this local verse-text export. To use an already extracted module:

```bash
python manage.py build_tagged_strongs_index svv --module-path /path/to/DutSVVA
```

## Source and attribution

- **Source module:** [CrossWire `DutSVVA` SWORD module ZIP](https://www.crosswire.org/ftpmirror/pub/sword/packages/rawzip/DutSVVA.zip), version 1.0.
- **Module catalogue:** [CrossWire raw SWORD packages](https://www.crosswire.org/ftpmirror/pub/sword/packages/rawzip/).
- **Module metadata:** `DistributionLicense=Public Domain`. The module's `About` field describes the Statenvertaling commissioned by the States-General and translated from the original languages following the Synod of Dordrecht (1618-1619).
- **Generator:** the parent project's `build_tagged_strongs_index` management command uses [pysword](https://github.com/fruiz500/pysword) to read the module and export plain verse text.

This export removes OSIS markup, study notes and headings, and packages the canonical verse text as compressed JSON. Preserve this provenance and public-domain declaration when republishing or regenerating the files. For the paired word-level Strong's data, see [`../tagged_bibles/README.md`](../tagged_bibles/README.md).
