# **Dataset Summary Box – MIMIC-IV Phenotype Atlas (MIPA)**

**Name:**
 MIMIC-IV Phenotype Atlas (MIPA)

**Purpose:**
 First **open-access benchmark dataset** for electronic health record (EHR) phenotyping, enabling reproducible comparison of ICD-based, NLP, ML, and LLM approaches.

**Source:**
 MIMIC-IV v2.2 (Beth Israel Deaconess Medical Center, 2008–2019).

**Unit of Analysis:**
 Hospital admission (1 discharge summary per admission).

**Size:**
 1,388 discharge summaries annotated across 16 phenotypes.

**Phenotypes:**
 Hypertension, Depression, Type II Diabetes, Type I Diabetes, Dementia, HFpEF, HFrEF, Metastatic Cancer, Rheumatoid Arthritis, Systemic Lupus, Alcohol Abuse, Obesity, DVT/PE (history/complication), C. difficile (history/complication), None.

**Annotation Process:**

- 2 annotators (physician + resident).
- Independent review → consensus adjudication.
- κ = 0.805 (document-level), κ = 0.771 (label-level).
- Rejection rate: 5% (ambiguous summaries excluded).

**Structured Features for ML:**

- **DIAG** (diagnoses), **PROC** (procedures), **MEDS** (prescriptions), **CHART** (chart events), **LABS** (labs), **DSNOTES** (discharge notes, UMLS-coded), **RADNOTES** (radiology notes, UMLS-coded).
- Feature export: one-hot pivot tables, CSV dictionaries.
- Cohorts: ICD-derived silver labels (train), gold-standard labels (val/test, 40/60 split).

**Benchmarking Case Study:**

- **LLMs:** Highest F1 in 13/16 phenotypes (average 0.85).
- **ICD heuristics:** Strong in well-coded diseases (HF, cancer).
- **TF-IDF:** Best for depression, hypertension.
- **Supervised ML:** Moderate performance, phenotype-specific.

**Access:**

- Labels: [GitHub](https://github.com/GRDS-M/MIMIC-IV-Phenotype-Atlas-Labels)
- Notes: via PhysioNet (CITI training + MIMIC-IV v2.2 access).
- Code: [GitHub](https://github.com/GRDS-M/MIMIC-IV-Phenotype-Atlas)

**Funding:**
 Fonds de recherche du Québec – Santé (FRQS).
