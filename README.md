# Academic Manuscript Skill

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill for generating publication-ready scientific manuscripts as Word documents (.docx) with real references, Zotero-compatible field codes, and journal-quality formatting.

## What It Does

- Generates structured scientific manuscripts (IMRaD, STAR Methods, etc.)
- Fetches **real citation metadata** from CrossRef using DOIs
- Injects **Zotero/Mendeley-style field codes** into Word documents — citations highlight grey on click, just like real reference manager output
- Supports multiple citation styles: Vancouver, APA, Nature
- Formats for major journals: Nature, Cell, The Lancet, PLOS ONE, IEEE

## Installation

### Claude Code (Plugin)

```bash
/plugin install kchemorion/academic-manuscript-skill
```

### Claude.ai

Go to **Settings > Capabilities > Skills**, upload the `skills/SKILL.md` file.

### Manual / Clone

```bash
git clone https://github.com/kchemorion/academic-manuscript-skill.git
```

### Dependencies

The scripts require Python with `requests`:

```bash
pip install requests
```

Document generation uses the Node.js `docx` package:

```bash
npm install -g docx
```

## Usage

### Full Pipeline

The skill runs a 4-stage pipeline:

1. **Content Assembly** — Structure manuscript sections from your input
2. **Reference Fetching** — Get real citation metadata from CrossRef
3. **Document Generation** — Build .docx with tables, formatting, and academic styling
4. **Zotero Field Injection** — Add Word field codes so citations behave like Zotero output

### Fetching References

Prepare a JSON file with your references:

```json
[
  {"id": 1, "doi": "10.1016/j.jacc.2020.11.010", "fallback": "Roth et al., JACC, 2020"},
  {"id": 2, "doi": null, "fallback": "Murray et al., Ann. Rheum. Dis., 2014"}
]
```

Then fetch metadata:

```bash
python3 scripts/fetch_references.py --input refs_input.json --output references.json --style vancouver
```

Supported styles: `vancouver` (default), `apa`, `nature`

You can also export BibTeX for import into Zotero/Mendeley:

```bash
python3 scripts/fetch_references.py --input refs_input.json --output refs.bib --format bibtex
```

### Injecting Zotero Field Codes

To add Zotero-style field codes to an existing manuscript:

```bash
# 1. Unpack the docx
python3 unpack.py manuscript.docx unpacked/

# 2. Inject field codes
python3 scripts/inject_zotero_fields.py --unpacked unpacked/ --refs references.json

# 3. Repack
python3 pack.py unpacked/ output.docx --original manuscript.docx
```

After injection, citations in Word will:
- Highlight grey when clicked (like real Zotero citations)
- Show underlying JSON when toggling with Alt+F9
- Have the bibliography wrapped in a `ADDIN ZOTERO_BIBL` field

## Repository Structure

```
├── .claude-plugin/
│   └── plugin.json                 # Plugin metadata
├── skills/
│   ├── SKILL.md                    # Full skill definition and documentation
│   ├── scripts/
│   │   ├── fetch_references.py     # CrossRef reference fetcher
│   │   └── inject_zotero_fields.py # Zotero field code injector
│   └── references/
│       └── journal-styles.md       # Formatting reference for major journals
└── README.md
```

## Supported Journals

| Journal | Citation Style | Abstract | Methods Placement |
|---------|---------------|----------|-------------------|
| Nature | Superscript | 150 words | After references |
| Cell | [N] numbered | 150 words (Summary) | STAR Methods |
| The Lancet | [N] numbered | 300 words, structured | Before results |
| PLOS ONE | [N] numbered | 300 words | Before results |
| IEEE | [N] numbered | 200 words | Before results |

See [`skills/references/journal-styles.md`](skills/references/journal-styles.md) for full details.

## License

MIT
