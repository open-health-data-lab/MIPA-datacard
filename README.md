# **MIMIC-IV Phenotype Atlas (MIPA) – Data Card**

## **Dataset Overview**

- **Name:** MIMIC-IV Phenotype Atlas (MIPA)
- **Domain:** Electronic Health Records (EHR) phenotyping
- **Source:** Derived from the publicly available MIMIC-IV v2.2 database (Beth Israel Deaconess Medical Center, 2008–2019).
- **Records:** 1,388 manually annotated discharge summaries.
- **Unit of Analysis:** Hospital admission (one discharge summary per admission).
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

### **Annotation Process**

- **Annotators:** Internal medicine physician + internal medicine resident.
- **Consensus Protocol:** Independent double review → joint deliberation → consensus or rejection if no adjudication.
- **Pre-consensus agreement:**
  - Document-level κ = **0.805** (near-perfect).
  - Label-level κ = **0.771** (substantial).
- **Rejections:** 71/1456 (≈5%) documents excluded after consensus due to unresolved ambiguity.
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
- One-hot encoded pivot tables (counts/admission) or time-series transformation.
- Low-frequency feature filtering (ICD/medications <25th percentile, UMLS concepts <10th percentile).
- Exports: CSV feature matrices + dictionary for interpretation.

**Cohorts:**

- **Silver labels:** Automatically derived using ICD heuristics
  - Patient-level indexes provided

- **Gold labels:** 1,388 annotated discharge summaries.
  - **Splits:** Validation/Test (40/60%),
  - Patient-level indexes provided

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
- **Clinical notes:**
   Access requires **CITI training** and **PhysioNet approval** for **MIMIC-IV v2.2**.
   Notes can be extracted using the provided script in `data/notes/notes.sql`.
- **EHR structured data:**
   Access requires **CITI training** and **PhysioNet approval** for **MIMIC-IV v2.2**.
   Parsing and processing pipelines are available in the **MIPA** repository:
   https://github.com/MIPA

------

## **Acknowledgments**

Supported by **Fonds de recherche du Québec – Santé (FRQS).**



# MIMIC-IV Phenotype Atlas (MIPA) – Data Card

**Citation (recommended):**  
Yamga E, et al. *MIMIC-IV Phenotype Atlas (MIPA): A Benchmark Dataset for EHR Phenotyping.* 

**DOI:**

**License:** Research use only (via PhysioNet + MIMIC-IV agreement)  

---

## Dataset at a Glance

- **Records:** 1,388 annotated discharge summaries  
- **Features:** ICD codes, procedures, medications, labs, chart events, UMLS-coded notes  
- **Splits:** Gold-standard validation/test (40/60); ICD-derived silver labels for training  
- **Access:** Labels on GitHub; EHR data and notes via PhysioNet and MIPA processing pipeline (CITI required)  