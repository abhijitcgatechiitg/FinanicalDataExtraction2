# Financial Data Extraction Pipeline

A multi-stage LLM-based tool to extract structured financial data from PDF documents.

## Quick Start

### 1. Install Dependencies
```bash
pip install -r requirements.txt
```

### 2. Set up Environment
Create a `.env` file with your Anthropic API key:
```
ANTHROPIC_API_KEY=your_key_here
```

---

## Pipeline Workflow

### Run the Complete Pipeline
Simply run the orchestrator with a PDF filename:

```bash
python main.py <pdf_filename>
```

**Example:**
```bash
python main.py sofp_sample3.pdf
```

This will automatically run:
1. **Step 1** - Extract text from PDF
2. **Step 2** - Classify pages to find SFP
3. **Step 3** - [Coming] Extract raw table data
4. **Step 4** - [Coming] Map to global schema
5. **Step 5** - [Coming] Validate financial data
6. **Step 6** - [Coming] Save final output

---

## Output Files

After running `python main.py sofp_sample3.pdf`, you'll find:

```
outputs/intermediate/
├── sofp_sample3_extracted.json      # Raw extracted pages
└── sofp_sample3_classified.json     # SFP classification results

outputs/final/
└── sofp_sample3_final.json          # [Coming] Final mapped schema
```

---

## Individual Step Reference (Advanced)

If needed, you can still run individual steps manually:

### Step 1: Extract Text from PDF
```bash
# This is called internally by main.py
# Located in: src/step1_pdf_extraction.py
```

### Step 2: Classify Pages (Find SFP)
```bash
# This is called internally by main.py
# Located in: src/step2_sfp_classifier.py
```

---

## Project Structure

```
src/
├── step1_pdf_extraction.py      # Extract text from PDF
├── step2_sfp_classifier.py      # Classify pages using LLM
├── step3_raw_extraction.py      # [Coming] Extract raw table data
├── step4_schema_mapping.py      # [Coming] Map to global schema
└── prompts/
    ├── classifier_prompt.py      # LLM prompt for SFP classification
    ├── extractor_prompt.py       # [Coming] LLM prompt for data extraction
    └── mapper_prompt.py          # [Coming] LLM prompt for schema mapping

outputs/
├── intermediate/                # Intermediate JSON files from each step
└── final/                        # Final mapped JSON output

schema/
├── global_schema.py             # Global schema definitions
```

---

## Next Steps

- [ ] Step 3: Extract raw table data to interim JSON (PASS 1)
- [ ] Step 4: Map interim JSON to global schema (PASS 2)
- [ ] Step 5: Validation layer
- [ ] Step 6: Save final output
- [ ] Optional: Streamlit UI for visualization

---

## Troubleshooting

**"ModuleNotFoundError: No module named 'anthropic'"**
- Run: `pip install -r requirements.txt`

**"ANTHROPIC_API_KEY not found in environment variables"**
- Make sure `.env` file exists in the project root with your API key

**"PDF file not found"**
- Check that the PDF exists in `sample_pdfs/` folder
- Use just the filename, not the full path
