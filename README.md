# HIGH YIELD — VIT Past Paper Analysis & ML Prediction

HIGH YIELD is a local-first educational prototype for collecting/analysing VIT past-paper PDFs, mapping questions to syllabus concepts, and exploring Decision Tree and K-Nearest Neighbours (KNN) estimates of concept recurrence.

> **Data integrity notice:** This repository does not bundle a verified public dataset. Papers must be sourced from the catalogue links below, and each PDF's subject, exam, year/slot and document type must be reviewed before treating it as historical exam data. The catalogue websites are source pages, not direct downloadable dataset links. Do not report a winning model unless evaluation on verified, held-out papers supports it.

## Data sources

- CodeChef-VIT past-paper catalogue: https://www.papers.codechefvit.com/
- ExamCooker / ACM-VIT CAT-1 catalogue: https://examcooker.acmvit.in/past_papers/exam/cat-1

There is currently **no single public dataset download URL** for the project's locally uploaded PDFs. Use the source catalogue links above to obtain papers where permitted. The project deliberately excludes uploaded PDFs and the local SQLite database from GitHub to avoid committing user files or unreviewed data. `data/paper_metadata_audit.csv` is a template/report location for recording provenance and manual verification.

## Idea

The models estimate whether a syllabus concept is represented in the labelled historical papers for a selected subject/exam. This can help students prioritise revision using evidence from past papers. It cannot guarantee which questions will appear in a future examination.

## Features

- Five subject syllabi and CAT-1, CAT-2 and FAT selectors.
- PDF upload, text extraction, question splitting and heuristic concept mapping.
- Manual review/correction workflow in the app.
- Decision Tree and KNN model implementation.
- Accuracy and confusion-matrix evaluation when the held-out dataset supports a meaningful binary comparison.
- Clear Previous Papers action in Settings.
- Windows launcher: `RUN_APP.bat`.
- Notebook: `HIGH_YIELD_ML_Analysis.ipynb`.

## Setup (Windows)

1. Download or clone the repository and extract it if downloaded as a ZIP.
2. Install a compatible Python version (Python 3.12 recommended).
3. Double-click `RUN_APP.bat`.
4. Choose subject and exam, upload papers, analyse them, review concept labels, and train the models.

The first launch may need internet access to install Python dependencies. Keep the server window open while using the app. Do not open `static/index.html` directly; the backend is required.

## Data preparation

The notebook loads processed paper records, syllabus concepts and concept matches from the app's local SQLite database (`data/high_yield.sqlite3`). It checks missing values and duplicates, creates paper–concept binary targets, and uses historical features. Automatic matches are weak labels: a missing match does not prove the concept is absent. Verify each PDF and concept mapping manually.

To create an audit report from the local database, run from the project root:

```bash
python scripts/audit_paper_metadata.py
```

The script flags missing metadata and marks documents as `UNVERIFIED`; it cannot determine whether a PDF is a genuine examination paper without human inspection. Fill source URL, document type, year/slot and verification fields after reviewing each file. Do not guess missing metadata.

## Models and evaluation

- **Decision Tree:** `DecisionTreeClassifier`.
- **KNN:** scaled features with `KNeighborsClassifier`.
- **Target:** whether a concept was matched in a given paper.
- **Evaluation:** paper-level/chronological holdout where possible; accuracy and confusion matrices are only meaningful when test labels include both classes and paper chronology is trustworthy.

If the dataset is too small, has only one target class, or has unreliable chronology, the notebook must report evaluation as **inconclusive**. A numerical score on an all-negative test set can look high while the model misses every positive example. No result is claimed in this README because the bundled records are not currently verified as genuine past examination papers with reliable metadata.

## Results and conclusion

**Current conclusion: no model winner can responsibly be reported yet.** First collect genuine papers, verify subject/exam/year/slot and document type, manually review concept labels, then run the notebook. Report the actual accuracy and confusion matrix produced by that run; do not fill in example scores as if they were results.

## GitHub publishing checklist

Commit source code, syllabus, notebook, README, dependency files, and scripts. Do **not** commit `.venv/`, uploaded PDFs, local database, processed files or generated model artefacts. `.gitignore` excludes those local files. Keep only public source URLs and metadata that you have permission to share.

This package is prepared for GitHub, but it has **not been pushed to a GitHub repository** because no repository URL or authorized GitHub connection was supplied. After creating a repository, upload/push these files and submit that repository URL.

## Project structure

- `app/main.py` — FastAPI backend, extraction, mapping, storage and ML endpoints.
- `static/` — single-page UI and styling.
- `data/syllabus.json` — five-subject syllabus.
- `data/paper_metadata_audit.csv` — metadata verification template/report.
- `scripts/audit_paper_metadata.py` — creates an audit report from the local DB.
- `HIGH_YIELD_ML_Analysis.ipynb` — notebook for data preparation, models and evaluation.
- `RUN_APP.bat` — Windows one-click launcher.
