# महाभारत XML Tool

A browser-based tool to check the flow of IDs of Shloka and Bhavadeepa, merge both XML files, and generate a report of which Bhavadeepa elements are not included in the main shloka file.

---

## Features

- **ID Transformation** — Converts Devanagari numerals in `shl_id` attributes to Roman numerals and restructures them into a consistent hierarchical format (`P01_U05_A023_S006`)
- **Bhavadeepa Merge** — Matches and injects `<bhavadeepa>` commentary elements into their corresponding `<shloka>` elements based on ID
- **Flow Validation** — Checks shloka sequences at every level (Parva → Upaparva → Adhyaya → Shloka) for gaps, duplicates, out-of-order entries, and cross-attribute mismatches
- **Unmerged Checker** — Lists all bhavadeepa IDs that could not be matched to any shloka in the main file
- **No dependencies** — Pure HTML/CSS/JS, runs entirely in the browser with no installation or internet connection required

---

## ID Format

| Level     | Format      | Example            |
|-----------|-------------|--------------------|
| Parva     | `P#`        | `P01`              |
| Upaparva  | `P##_U##`   | `P01_U05`          |
| Adhyaya   | `…_A###`    | `P01_U05_A023`     |
| Shloka    | `…_S###`    | `P01_U05_A023_S006`|
| Bhavadeepa| `…_S###_B##`| `P01_U05_A023_S006_B01`|

---

## Usage

### Recommended — via Apache (localhost)

If you have Apache installed, place the file in your web root:

```bash
sudo cp mahabharata_xml_tool.html /var/www/html/
sudo systemctl start apache2
```

Then open in your browser:

```
http://localhost/mahabharata_xml_tool.html
```

### Alternative — Python local server

```bash
python3 -m http.server 8000
```

Then open:

```
http://localhost:8000/mahabharata_xml_tool.html
```

> **Note:** Do not open the file directly via `file://` in Chrome — Chrome blocks programmatic downloads from local file pages. Firefox works fine without a server.

---

## How to Use

1. Open the tool in your browser via `localhost`
2. Upload your **Main Shloka XML** file (contains `<shloka>` elements with Devanagari `shl_id` attributes)
3. Upload your **Bhavadeepa Commentary XML** file (contains `<bhavadeepa>` elements)
4. Click **Transform · Merge · Validate**
5. Review the results — validation errors grouped by type, unmerged IDs listed individually
6. Download your output files using the buttons provided

---

## Output Files

| File | Description |
|------|-------------|
| `*_transformed.xml` | Main shloka file with IDs converted to the new format |
| `*_bhavadeepa_transformed.xml` | Bhavadeepa file with IDs converted |
| `*_merged.xml` | Final file — transformed shlokas with bhavadeepa commentary injected |
| `mbh_processing_report.txt` | Full text report — errors, unmerged IDs, and structure summary |

---

## Expected XML Structure

**Main Shloka XML**
```xml
<root>
  <parva id="P01">
    <upaparva id="P01_U01">
      <adhyaya id="P01_U01_A001">
        <shloka shl_id="१">...</shloka>
        <shloka shl_id="२">...</shloka>
      </adhyaya>
    </upaparva>
  </parva>
</root>
```

**Bhavadeepa XML**
```xml
<root>
  <adhyaya id="P01_U01_A001">
    <bhavadeepa shl_id="१">...</bhavadeepa>
  </adhyaya>
</root>
```

**Merged Output**
```xml
<shloka shl_id="P01_U01_A001_S001">
  ...shloka content...
  <bhavadeepa bhavadeepa_id="P01_U01_A001_S001_B01">
    ...commentary...
  </bhavadeepa>
</shloka>
```

---

## Validation Checks

| Check | Description |
|-------|-------------|
| Missing | Gaps in sequence at any level (e.g. S003 followed by S005) |
| Duplicate | Same ID appearing more than once |
| Out-of-order | A shloka number lower than the previous one |
| Mismatch | Shloka's `shl_id` prefix does not match its enclosing parva/upaparva/adhyaya attributes |

---

## Python Scripts

The repository also includes the original Python scripts used before the browser tool was built. These require `lxml` and are suited for server-side or batch processing.

| Script | Purpose |
|--------|---------|
| `transform_shloka_id.py` | Transforms all XML files in an input directory |
| `merge_mahabharata.py` | Merges bhavadeepa commentary into the main XML |
| `validate_shloka_flow.py` | Validates shloka ID flow and saves a report |

Install dependency:
```bash
pip install lxml
```

---

## Project Context

This tool is part of a digital critical edition project for the Mahabharata, integrating the root text with Nilakantha Chaturdhara's *Bhāratabhāvadīpa* — a 17th-century Sanskrit commentary. The structured ID format enables precise cross-referencing between the main text and commentary at the shloka level.

---

## License

MIT License — free to use, modify, and distribute. 
