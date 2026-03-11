# E-Commerce Sales Data Cleaning Pipeline

**Author:** Hassan Akhter  
**Language:** Python  
**Libraries:** pandas, NumPy, re, matplotlib

## What is this project?

I built a data cleaning pipeline on a messy e-commerce dataset. The raw data had all kinds of real-world problems — duplicate orders, missing values, prices with currency symbols, dates in 5 different formats, invalid emails, and inconsistent text casing.

I cleaned all of it step by step using Python and pandas, and exported a clean CSV ready for analysis or model training.


## Why I built this

Data cleaning is one of the most important skills in data work. Before you can analyse anything or train any model, the data has to be trustworthy. I wanted to build a project that shows I understand not just *how* to clean data, but *why* each decision is made.


## The Dataset

- **File:** `ecommerce_dirty.csv`
- **Rows:** 525 (raw) → 500 (after cleaning)
- **Columns:** 11
- **Domain:** E-commerce orders

### Problems in the raw data

| Problem | Detail |
|---|---|
| Duplicate rows | 25 exact duplicate orders |
| Missing values | Nulls across name, email, phone, price, date, status, country |
| Inconsistent casing | "electronics", "ELECTRONICS", "Electronics" |
| Mixed date formats | 5+ formats: DD/MM/YYYY, YYYY-MM-DD, DD Mon YYYY etc. |
| Currency symbols | Prices stored as "$1341.68" and "£90.83" — not usable as numbers |
| Invalid emails | Missing @, no domain, placeholder strings |
| Wrong data types | Quantity stored as float instead of integer |

## What I did — Step by Step

**Step 1: Remove Duplicates**  
Dropped 25 exact duplicate rows using `drop_duplicates()`.

**Step 2: Customer Name**  
Stripped whitespace, standardised to Title Case, filled nulls with "Unknown".

**Step 3: Email**  
Wrote a custom `is_valid_email()` function that checks for exactly one @, a non-empty username and domain, a dot in the domain, and no spaces. Invalid emails replaced with `unknown@unknown.com`.

**Step 4: Phone Number**  
Stripped all non-digit characters using regex, then reformatted as XXX-XXX-XXXX if exactly 10 digits. Anything unparseable left as NaN — a fake phone number is worse than no phone number.

**Step 5: Category**  
Standardised to Title Case, filled nulls with the most common category (mode).

**Step 6: Price**  
Stripped currency symbols ($, £) using regex, converted to float. Filled missing prices with the **median** — not mean, because median is less affected by extreme values.

**Step 7: Outlier Removal (IQR Method)**  
Used the IQR method to define a valid price range:
`Q1 - 1.5×IQR` to `Q3 + 1.5×IQR`. 
Anything outside that range removed as a likely data entry error.

**Step 8: Quantity**  
Filled nulls with median, converted from float back to integer (`Int64` — pandas nullable integer type).

**Step 9: Order Date**  
Used `pd.to_datetime(dayfirst=True, errors="coerce")` to handle 5+ date formats automatically. Unparseable dates filled with the mode.

**Step 10: Order Status**  
Standardised to Title Case, filled nulls with "Unknown".

**Step 11: Country**  
Standardised to Title Case, fixed abbreviations (USA, UK), filled nulls with "Unknown".


## Before vs After

| Metric | Before | After |
|---|---|---|
| Rows | 525 | 500 |
| Duplicates | 25 | 0 |
| Missing values (price) | 32 | 0 |
| Missing values (email) | 21 | 0 |
| Missing values (country) | 18 | 0 |
| Phone nulls | 196 | 186* |

*186 phone nulls remain intentionally — these are genuinely missing numbers, not a cleaning error.


## Output

- **File:** `ecommerce_clean.csv`
- **Rows:** 500
- **Duplicates:** 0
- **Ready for:** Analysis, database ingestion, or model training


## Files in this repo

| File | Description |
|---|---|
| `data_cleaning.ipynb` | Main notebook with all cleaning steps |
| `ecommerce_dirty.csv` | Raw messy dataset |
| `ecommerce_clean.csv` | Final cleaned output |


## What I learned

- Median is better than mean for filling missing numerical values when outliers are present
- It's sometimes better to leave nulls than fill them with fake data (phone numbers)
- The IQR method is more reliable than fixed thresholds for outlier detection because it adapts to your data
- Cleaning decisions should always be documented with a reason, not just applied silently
