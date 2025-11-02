 Learn On Your Own — **LCCF + Study Sheet Generator**

> A clean, notebook‑driven workflow to generate polished **problem sheets** (Codeforces & LeetCode) and **study sheets** from topic lists — fully compiled to **PDF** with XeLaTeX.

**Includes:**
- `LCCF/mega_solutions_overleaf.ipynb` — problem‑sheet generator (reads a mega problems CSV; supports CF + LC).
- `Study Sheet Generator/Study_Sheet_OnePrompt.ipynb` — study‑sheet generator (reads a topics CSV).

## 📚 Table of Contents

- [📚 Table of Contents](#-table-of-contents)
- [✨ Features](#-features)
- [📁 Repository Layout](#-repository-layout)
- [🚀 Quick Start](#-quick-start)
  - [1) Environment](#1-environment)
  - [2) API keys (.env)](#2-api-keys-env)
  - [3) Run the notebooks](#3-run-the-notebooks)
- [🧭 How It Works](#-how-it-works)
  - [LCCF — Problem Sheet Generator](#lccf--problem-sheet-generator)
  - [Study Sheet Generator](#study-sheet-generator)
- [⚙️ Configuration Reference](#️-configuration-reference)
- [❓ FAQ](#-faq)

---

## ✨ Features

- **CSV → PDF pipeline**: Ingest a single CSV of problems or topics, emit **XeLaTeX** and **PDF**.
- **Multi‑platform**: Works with **Codeforces** (contestId/index) and **LeetCode** (id/slug) rows in the same CSV.
- **Friendly filenames**: Slugged names like `problem-sheet-cf-two-sum.pdf` or `study-sheet-11-lu-and-qr-factorizations.pdf`.
- **Robust LaTeX build**: Uses `latexmk -xelatex` with sensible line‑breaking and fonts.

---

## 📁 Repository Layout

```text
learnonyourown/
  LCCF/
    mega_problems.csv            # problem catalog (CF + LC supported)
    mega_problems-full.csv       # larger variant (optional)
    mega_solutions_overleaf.ipynb
    out_pdfs/
      problems/                  # generated TeX/PDF/logs per problem
  Study Sheet Generator/
    Study_Sheet_OnePrompt.ipynb
    topics.csv                   # list of study topics
    out_pdfs/
      topics/                    # generated TeX/PDF/logs per topic
```

---

## 🚀 Quick Start

### 1) Environment
```bash
# Python (recommended)
python -m venv .venv && . .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -U openai pandas tqdm python-dotenv jupyter

# LaTeX (required for PDF) — install latexmk + XeLaTeX:
# • macOS:   brew install --cask mactex       # then `tlmgr install latexmk`
# • Ubuntu:  sudo apt-get install texlive-full latexmk
# • Windows: install MiKTeX and enable latexmk/xelatex in PATH
```

### 2) API keys (.env)
Create a `.env` file **next to the notebook you are running** (at minimum for `LCCF/`):

```dotenv
OPENAI_API_KEY=sk-...
# Optional tuning:
MODEL_NAME=gpt-5
MODEL_MAX_OUTPUT_TOKENS=120000
OUT_DIR=out_pdfs            # default output root for LCCF
LIMIT_ROWS=10               # process only first N rows (optional)
MEGA_PROBLEMS_CSV=mega_problems.csv
```

### 3) Run the notebooks
Open in Jupyter/VS Code and **Run All Cells**:

```bash
jupyter notebook
# or: code .   # and open the .ipynb files
```

---

## 🧭 How It Works

### LCCF — Problem Sheet Generator
**Notebook:** `LCCF/mega_solutions_overleaf.ipynb`

**Inputs**
- Problems CSV (default: `LCCF/mega_problems.csv`; overridable via `MEGA_PROBLEMS_CSV`).
- Expected columns (flexible mapping handled): `site, problem_id, contestId, index, title, url, tags, rating, statement_text, slug, difficulty, paid_only`.

**Column mapping** (auto‑detected per row):
```python
canonical_problem_dict = {
  'platform'  : one_of('platform','site','source'),     # e.g., 'cf' or 'lc'
  'title'     : one_of('title','name'),
  'statement' : one_of('statement_text','statement','text','problem','body','description'),
  'url'       : one_of('url','link','href'),
  'difficulty': one_of('difficulty','level'),
  'rating'    : one_of('rating'),                     # CF rating if available
  'lc_id'     : one_of('id','lc_id','leetcode_id','frontend_id'),
  'contest_id': one_of('contestId','contest_id'),
  'index'     : one_of('index','problem_index'),
}
```

**Outputs** (per problem):
- TeX: `LCCF/out_pdfs/problems/problem-sheet-<platform>-<title>.tex`
- PDF: `LCCF/out_pdfs/problems/problem-sheet-<platform>-<title>.pdf`
- Build log: `LCCF/out_pdfs/problems/problem-sheet-<...>.build.txt`

**Examples from your repo:**
- `LCCF/out_pdfs/problems/problem-sheet-cf-...dary.pdf`
- `LCCF/out_pdfs/problems/problem-sheet-cf-1.pdf`
- `LCCF/out_pdfs/problems/problem-sheet-cf-2-doors.pdf`

**Build toolchain**
- Compiles with `latexmk -xelatex` (falls back to multiple `xelatex` passes if needed).

---

### Study Sheet Generator
**Notebook:** `Study Sheet Generator/Study_Sheet_OnePrompt.ipynb`

**Inputs**
- Topics CSV (default: `Study Sheet Generator/topics.csv`). One topic per row is fine.

**Outputs** (per topic):
- TeX: `Study Sheet Generator/out_pdfs/topics/study-sheet-<n>-<topic>.tex`
- PDF: `Study Sheet Generator/out_pdfs/topics/study-sheet-<n>-<topic>.pdf`
- Build log: `Study Sheet Generator/out_pdfs/topics/study-sheet-<...>.build.txt`

**Examples from your repo:**
- `Study Sheet Generator/out_pdfs/topics/study-sheet-1-vector-spaces-and-subspaces.pdf`
- `Study Sheet Generator/out_pdfs/topics/study-sheet-10-row-echelon-form-and-solutions-of-linear-systems.pdf`
- `Study Sheet Generator/out_pdfs/topics/study-sheet-11-lu-and-qr-factorizations.pdf`

---

## ⚙️ Configuration Reference

Set via `.env` (preferred) or edit the **Config** cell in each notebook.

**Common**
- `OPENAI_API_KEY` — required for model generation.
- `MODEL_NAME` — default `gpt-5`.
- `MODEL_MAX_OUTPUT_TOKENS` — default `120000`.

**LCCF‑specific**
- `MEGA_PROBLEMS_CSV` — path to the problems CSV (default `mega_problems.csv`).
- `OUT_DIR` — base output directory (default `out_pdfs`).
- `LIMIT_ROWS` — optionally cap the number of processed rows.

> Tip: On Windows, avoid hard‑coding absolute paths; keep paths **relative to the notebook folder** for portability.

---

## ❓ FAQ

**Where do files get saved?**
- Problems → `LCCF/out_pdfs/problems/`
- Topics   → `Study Sheet Generator/out_pdfs/topics/`

**Do I need TeX installed?**
- Yes. Install a TeX distribution with `latexmk` and `xelatex` (see Quick Start).

**How do I include LeetCode problems?**
- Add LC rows to `mega_problems.csv` (set `platform` to `lc`, include `id`/`slug`/`url`, and a `statement` if you have it). The generator will format them just like CF problems.

**Can I limit generation?**
- Yes: set `LIMIT_ROWS` (e.g., `LIMIT_ROWS=5`) to process only a subset while testing.

---

