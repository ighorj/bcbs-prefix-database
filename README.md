# BCBS Prefix Database Project

## Overview

This project builds a structured database of Blue Cross Blue Shield (BCBS) prefix codes.

The workflow includes:

1. Programmatic data collection using Python
2. Data cleaning and normalization
3. Storage in PostgreSQL
4. Exporting structured CSV datasets for analytics and integration use

---

## Tech Stack

- Python 3
- PostgreSQL
- Pandas
- SQL
- Git

---

## Project Workflow

### 1️⃣ Data Collection (Python)

Raw prefix data was collected and parsed using Python scripts.

Key steps:
- Read raw prefix files
- Parse prefix + payer mappings
- Normalize multi-line entries
- Remove noise (phone numbers, metadata)
- Validate prefix format (3-character alphanumeric)

## Why I built this ?


I built this project after repeatedly seeing how often **BCBS prefixes are misidentified in real-world workflows** — especially in companies that are integrating AI into their intake and insurance verification processes.

In practice, clinics and front-desk staff frequently enter the wrong BCBS location. And not just occasionally — it happens a lot. The issue is that “BCBS” isn’t a single entity. Each three-character prefix corresponds to a specific regional payer, and that distinction matters for eligibility checks, claims routing, reimbursement logic, and downstream automation.

AI systems struggle when the underlying data is inconsistent or ambiguous. If the prefix-to-payer mapping isn’t structured, normalized, and queryable, the AI layer ends up inheriting human error instead of correcting it.

This project creates a clean, indexed, structured BCBS prefix database that can be integrated directly into AI-driven workflows. By doing so, it allows companies to:

- **Validate prefixes automatically**

- **Correct misidentified payers**

- **Route claims more accurately**

- **Reduce manual rework and denials**

- **Improve training data quality for AI systems**

The goal wasn’t just to store prefix data — it was to **build infrastructure that reduces preventable operational errors and makes AI systems more reliable in real healthcare environments**.

---

## 2️⃣ Data Ingestion – Initial Web Extraction

The script below demonstrates how I extracted structured prefix information from a payer directory source and wrote it into a CSV file.

```python
from urllib.request import urlopen
from bs4 import BeautifulSoup
import re
from pathlib import Path

# Fetch page (We picked ZAA-TO-ZZZ as an example but that can be done to all others like AAA - AAZ
html = urlopen(
    'https://mypayerdirectory.com/bcbs-prefix-list/bcbs-alpha-prefixes-zaa-to-zzz/'   
)
soup = BeautifulSoup(html.read(), 'html.parser')

# Find all <td> whose class starts with "column-"
cells = soup.find_all('td', class_=re.compile(r'^column-'))

# Write extracted text to a .txt file
path = Path('ZAA-ZZZ2.csv')

with path.open('w', encoding='utf-8') as f:
    f.write("Prefix,Name\n") 

    for cell in cells:
        text = cell.get_text(strip=True)

        if text:  # skip empty cells
            f.write(text + ',\n')

print("Done. Data written")
```

### 3️⃣ Database Storage (PostgreSQL)

Data was inserted into a structured PostgreSQL table.

Schema:

```sql
CREATE TABLE bcbs_prefixes (
    id SERIAL PRIMARY KEY,
    prefix VARCHAR(3) NOT NULL,
    payer_name TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
