# Project Planning Document

## Project Overview
**Project Name:** Financial Data Extraction
**Description:** 
I want to build a LLM based data extraction tool which inputs various pdfs from different financial audits of companies. Usuall pdfs contains company information, Balance Sheets, Profit and loss statements, cash flow etc. My goal is to fetch these pages information in a structure json format which can be leveraged later down the pipeline.

**Problem Statement:**
Extracting financial data information from pdfs

---

## Technology Stack
**Backend:** Python, Langraph, LLM, pyMuPdf
LLM MODEL: claude-3-haiku-20240307 (only this model is available to me)
**Frontend:** Not decided yet, I will probably use streamlit
**Database:** Not right now, mostly working with json output files

**Other Tools/Libraries:** [Any other important technologies]


## Project Structure
I want a simple architecture for now, a single main.py file which can complete the flow. There will be few folders for intermidiate output and final output. This project will contain LLM prompts, so I want each prompt to be in a separate .py file


## **Instructions:** 
Here is the complete idea that I have which can be still be changed if necessery.
I have multiple stages planned for this project, 
Step 1 — Read the PDF and extract raw text
    Use PyMuPDF to extract text from each page.
    We simply get a list of pages with their text.
    Output: pages = [{page_number, text}, ...]

Step 2 — Find the Section We Care About
    Ideally here I will be extracting all the section separatly including balance sheet, p&l and cashflow but to make it simpler Right now we care only about Balance Sheet / Statement of Financial Position (SFP).
    Send each page’s text to an LLM classifier.
    The model returns:
    → “Does this page contain SFP? Yes/No”
    We collect the pages marked as SFP.

    Output: sfp_text (could be 1 or more pages combined later)
Step 3 — from now, we work as  two pass LLM calls
1st call, tells the llm to read data exactly as it is without forcing any global schema logic here. Just the raw fields from the section data. this generates a interim json file.
2nd call, we tell the LLM to map the interim json to a fixed general global schema for that section like balance sheet. 

    PASS 1 — Extract RAW TABLE (interim JSON)
        We give the LLM the section text only, NOT the whole PDF.
        LLM returns a generic, flexible JSON that represents the table AS IT IS (without forcing schema).   
        This JSON contains:
            - section → e.g., "SFP"
            - years → detected year columns
            - currency
            - items → list of rows

        Each item has:
            label_raw → exact row name from PDF
            category_raw → Assets / Liabilities / Equity (if detectable)
            is_total → true/false
            values → dictionary of year:value
            extra → any additional data (notes, original text)

        Very important:
        👉 We DO NOT force business field names here.
        👉 Any weird/unexpected PDF row is allowed.

        This is your universal table format.

        Output: interim.json

    PASS 2 — Map RAW → GLOBAL SCHEMA

    Now we give the LLM:
    The interim JSON
    Your strict global schema (fixed names)
    
    Mapping rules:
        Do NOT invent fields
        Only use values from interim
        If unsure → put inside "unmapped_items"

    The model responds with:
        mapped_schema → your strict schema filled where it can
        unmapped_items → items that didn’t match

    This is where my final structured JSON is created.

    Output: mapped.json

Step 4 — Add Optional Intelligence (later)
    We can help the model by:
    Giving alias dictionary hints (e.g., “Tangible assets → PPE”)
    Giving fuzzy-matching suggestions
    Few-shot mapping examples
 *Not mandatory now — optional improvement path.

Step 5 — Validation Layer
    After mapping, we run Python-side validation:
    Do Assets = Equity + Liabilities?
    Do subtotals ≈ sum of children?
    Are all fields numeric?
    Are critical fields missing?
    Year consistency check

Step 6 — Save Final Output


Note:
I have created a temporary global schema in schema folder as "balance_sheet_global_schema.py"which I think looks good but can be made simpler so that it works for a variety of different pdf. In this folder I will later add all the other schemas like p&l and cash flow.



**Last Updated:** 2025-12-05