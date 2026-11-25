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
 1,388 discharge summaries (1,011 patients) annotated across 16 phenotypes.

**Phenotypes:**
 Hypertension, Depression, Type II Diabetes, Type I Diabetes, Dementia, HFpEF, HFrEF, Metastatic Cancer, Rheumatoid Arthritis, Systemic Lupus, Alcohol Abuse, Obesity, DVT/PE (history/complication), C. difficile (history/complication), None.

**Annotation Process:**

- 2 annotators (physician + medical student, now a resident).
- Independent review → consensus adjudication; summaries with ≥3 discordant labels (excluding Obesity, Depression, None) excluded without review.
- κ = 0.805 (document-level), κ = 0.771 (label-level).
- Exclusions: 70/1,458 (4.8%): 31 by the rejection rule, 39 without consensus.

**Structured Features for ML:**

- **DIAG** (diagnoses), **PROC** (procedures), **MEDS** (prescriptions), **CHART** (chart events), **LABS** (labs), **DSNOTES** (discharge notes, UMLS-coded), **RADNOTES** (radiology notes, UMLS-coded).
- Feature export: one-hot pivot tables, CSV dictionaries.
- Cohorts: ICD-derived silver labels (train), gold-standard labels (val/test, 50/50 per phenotype at the admission level; patient-grouped split also provided).

**Benchmarking Case Study:**

Illustrative comparison; several differences fall within 95% confidence intervals.

- **GPT-4o (zero-shot):** Highest F1 in 12/16 phenotypes (mean 0.85).
- **ICD heuristics:** Strong in well-coded diseases (HF, cancer).
- **Keyword TF-IDF:** Best for depression and obesity.
- **Supervised ML (silver labels):** Moderate performance (mean F1 0.43), phenotype-specific.

**Access:**

- Labels: [GitHub](https://github.com/GRDS-M/MIMIC-IV-Phenotype-Atlas-Labels)
- Notes: via PhysioNet (CITI training + MIMIC-IV v2.2 access).
- Code: [GitHub](https://github.com/GRDS-M/MIMIC-IV-Phenotype-Atlas)

**Funding:**
 Fonds de recherche du Québec – Santé (FRQS).
