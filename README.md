# **MIMIC-IV Phenotype Atlas (MIPA) – Data Card**

## **Dataset Overview**

- **Name:** MIMIC-IV Phenotype Atlas (MIPA)
- **Domain:** Electronic Health Records (EHR) phenotyping
- **Source:** Derived from the publicly available MIMIC-IV v2.2 database (Beth Israel Deaconess Medical Center, 2008–2019).
- **Records:** 1,388 manually annotated discharge summaries from 1,011 patients.
- **Unit of Analysis:** Hospital admission (one discharge summary per admission; some patients contribute more than one admission).
- **Goal:** Provide a **publicly available benchmark dataset** for reproducible and fair evaluation of EHR phenotyping methods.

## **Motivation**

Secondary use of EHRs requires converting raw clinical information into research-grade, structured data. A key step is phenotyping—the identification of patient cohorts based on medical conditions.
 While many methods exist (ICD heuristics, supervised ML, LLMs), progress has been hindered by the lack of standardized, multi-phenotype benchmark datasets.
MIPA fills this gap by providing gold-standard, manually annotated labels across 16 phenotypes of varying prevalence and complexity, enabling reproducible comparison of methods.

------

## **Dataset Composition**

- **Phenotypes (n=16):**
  - **High prevalence:** Hypertension, Depression, Type II Diabetes
  - **Low prevalence:** Type I Diabetes, Dementia, Systemic Lupus, HFpEF, HFrEF, Rheumatoid Arthritis, Metastatic Cancer
  - **Lifestyle-Associated Conditions**: Alcohol abuse, Obesity
  - **Healthcare-Associated Events:** DVT/PE (history), DVT/PE complication, C. difficile (history),  C. difficile complication
- **Negative label:** “None” (absence of any of the above phenotypes).
- **Operational definitions** (full definitions in Supplementary Table S1 of the paper):
  - *Metastatic cancer:* documented metastatic or stage IV solid malignancy.
  - *Complication* labels (C. difficile, DVT/PE): an active episode during the index admission, whether present on admission or hospital-acquired. *History* labels: a prior episode.
  - *Depression:* documented history of depression, or an antidepressant prescription unless another indication is documented.
- **Missing label:** one summary has no HFrEF label (HFrEF n = 1,387).

### **Annotation Process**

- **Annotators:** An internal medicine physician and a medical student (now an internal medicine resident).
- **Consensus Protocol:** Independent double review of 1,458 candidate summaries → joint deliberation of discordant summaries → consensus, or exclusion if none was reached.
- **Rejection rule:** summaries with three or more discordant labels, not counting Obesity, Depression and None, were excluded without joint review. (The three exempted labels were set after the first annotation pass.)
- **Pre-consensus agreement:**
  - Document-level κ = **0.805** (near-perfect).
  - Label-level κ = **0.771** (substantial).
- **Exclusions:** 70/1,458 (4.8%): 31 under the rejection rule and 39 without consensus after joint review. Consensus was reached for 718 of the 788 summaries with any disagreement (91%).
- **Accepted:** Final dataset of **1,388 discharge summaries**.



------

## **Dataset Statistics**

From **Table 1 (Overall Characteristics)**:

- **Admissions:** 1,388
- **Age:** 59.8 ± 16.4 years (median 61, range 19–97)
- **Sex:** 59.4% female, 40.6% male
- **Race/Ethnicity:** 62.4% White, 22.0% Black/African American, 2.1% Other, with smaller subgroups (Puerto Rican, Caribbean, Russian, etc.)
- **Discharge summary characteristics:**
  - Tokens per note: mean 2243 ± 809 (range 539–5855)
  - Sentences per note: mean 127 ± 50
  - Sections: near-universal coverage of all core discharge summary sections (PMH 98.6%, Allergies 100%, Medications 98.1%).

------

## **Structured Dataset for Supervised Learning**

In addition to annotated discharge notes, MIPA provides a **structured feature dataset** built from MIMIC-IV tables:

- **DIAG** – Diagnoses (`diagnoses_icd`, mapped ICD-9 → ICD-10)
- **PROC** – Procedures (`procedures_icd`)
- **MEDS** – Medications (`prescriptions`, mapped via NDC)
- **CHART** – Charted events (`chartevents`)
- **LABS** – Laboratory results (`labevents`)
- **DSNOTES** – Discharge notes (UMLS-coded with MedCAT)
- **RADNOTES** – Radiology notes (UMLS-coded with MedCAT)

**Processing pipeline:**

- Named entity recognition via **MedCAT** → UMLS CUIs.
- Pivot tables of counts per admission, divided by length of stay in hours.
- Low-frequency feature filtering (features below the 25th percentile of counts; UMLS concepts below the 20th percentile), computed within each phenotype's training cohort.
- Exports: CSV feature matrices + dictionary for interpretation.

**Cohorts:**

- **Silver labels (training):** derived from ICD codes; positives and controls sampled in approximately equal numbers (50–62% positive). Patients contributing a gold-labelled summary are excluded.
  - Admission-level indexes per phenotype: `data/cohort/train/indexes/`

- **Gold labels (validation/test):** 1,388 annotated discharge summaries.
  - **Splits used in the paper:** per phenotype, 50/50 validation/test, stratified by the gold label (seed 42), at the admission level: `data/cohort/test_val/indexes/`. Nineteen summaries from patients aged ≥89 years are not included (1,369 per phenotype). Because the split is by admission, some patients have summaries in both halves.
  - **Patient-grouped split:** a single 50/50 split of all 1,388 summaries, shared by all phenotypes, with every patient's summaries in the same half: `data/cohort/test_val/patient_grouped_split.csv` (generated by `benchmark/make_patient_split.py` in the MIPA repository).

------

## **Use Cases**

- Benchmarking phenotyping algorithms across methods (ICD, keyword-based, ML, LLM).
- Training and validating supervised ML models on structured EHR features.

## **Limitations**

- **Unicentric dataset:** Derived from a single academic center (Beth Israel Deaconess Medical Center). May not generalize to other settings.
- **Scope:** Only discharge summaries annotated (no outpatient or nursing notes in MIMIC-IV).

## **Access & Licensing**

- **Labels:**
   Openly available in this repository under `data/annotations/labels.csv`.
- **Cohort splits:**
   Indexes for training, validation, and test sets are available for all phenotypes in:
  - `data/cohort/train/indexes/`
  - `data/cohort/test_val/indexes/`
  - `data/cohort/test_val/patient_grouped_split.csv`
- **Benchmark inputs:**
  - `data/benchmark/gpt4o_predictions.csv`: GPT-4o zero-shot predictions (0/1) for every summary and phenotype, as used in the paper (no note text).
  - `data/benchmark/icd_codes.sql`: query extracting the ICD codes of the gold admissions (credentialed access).
  - Benchmark scripts: `benchmark/` in the [MIPA repository](https://github.com/open-health-data-lab/MIPA).
- **Clinical notes:**
   Access requires **CITI training** and **PhysioNet approval** for **MIMIC-IV v2.2**.
   Notes can be extracted using the provided script in `data/notes/notes.sql`.
- **EHR structured data:**
   Access requires **CITI training** and **PhysioNet approval** for **MIMIC-IV v2.2**.
   Parsing and processing pipelines are available in the **MIPA** repository:
   https://github.com/open-health-data-lab/MIPA

------

## **Acknowledgments**

Supported by **Fonds de recherche du Québec – Santé (FRQS).**



# MIMIC-IV Phenotype Atlas (MIPA) – Data Card

**Citation (recommended):**  
Yamga, E., Goudrar, R. & Després, P. *MIMIC-IV Phenotype Atlas (MIPA) Datacard.* GitHub repository (2025). https://github.com/open-health-data-lab/MIPA-datacard 

**DOI:**

**License:** Research use only (via PhysioNet + MIMIC-IV agreement)  

---

## Dataset at a Glance

- **Records:** 1,388 annotated discharge summaries  
- **Features:** ICD codes, procedures, medications, labs, chart events, UMLS-coded notes  
- **Splits:** Gold-standard validation/test (50/50 per phenotype, admission level; patient-grouped split also provided); ICD-derived silver labels for training  
- **Access:** Labels on GitHub; EHR data and notes via PhysioNet and MIPA processing pipeline (CITI required)  

---

## Changelog

**November 2026 (journal revision)**
- Annotation: annotator status, rejection rule and exclusion counts corrected (70 of 1,458 excluded; previously reported as 71 of 1,456). The 1,388 labels are unchanged.
- Splits: the gold validation/test split is 50/50 per phenotype at the admission level (previously described as 40/60, patient level). The index files are unchanged. Added `patient_grouped_split.csv` for patient-level separation.
- Features: UMLS concept filter is the 20th percentile (previously described as 10th); counts are normalised by length of stay.
- Added operational definitions for metastatic cancer, complication versus history labels, and depression.
- Added GPT-4o predictions and the ICD extraction query, so that the zero-shot benchmark can be reproduced.
