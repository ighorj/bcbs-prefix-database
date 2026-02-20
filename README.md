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

---

### 2️⃣ Database Storage (PostgreSQL)

Data was inserted into a structured PostgreSQL table.

Schema:

```sql
CREATE TABLE bcbs_prefixes (
    id SERIAL PRIMARY KEY,
    prefix VARCHAR(3) NOT NULL,
    payer_name TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
