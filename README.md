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
# 🖼 Sample Outputs (snippets)

<details>
<summary><strong>📄 LCCF Problem Sheet — LeetCode “Insert Interval”</strong> (snippet)</summary>

**Saved as:** `LCCF/out_pdfs/problems/problem-sheet-lc-insert-interval.pdf`

```tex
% --- Problem Header ---
\section*{Problem: Insert Interval (LC 57)}
\textbf{Difficulty:} Medium \quad
\textbf{URL:} \texttt{https://leetcode.com/problems/insert-interval/}

% --- ELI5 Intuition ---
\paragraph{ELI5.}
Walk from left to right. Copy intervals that end before the new one starts.
Merge overlaps into one growing interval. Append the rest.

% --- Formal Solution ---
\paragraph{Solution.}
Let new interval be $[s, e]$. For each $[l_i, r_i]$ in order:
1) If $r_i < s$, push $[l_i, r_i]$.
2) Else if $e < l_i$, push $[s, e]$ once, then push $[l_i, r_i]$ and the remaining.
3) Else (overlap), set $s \leftarrow \min(s,l_i)$ and $e \leftarrow \max(e,r_i)$ and continue.

\paragraph{Complexity.} \mathcal{O}(n) time, \mathcal{O}(1) extra space.
```

```python
# Python (from the PDF)
def insert(intervals, new):
    s, e = new
    out, placed = [], False
    for l, r in intervals:
        if r < s:
            out.append([l, r])
        elif e < l:
            if not placed:
                out.append([s, e]); placed = True
            out.append([l, r])
        else:
            s, e = min(s, l), max(e, r)
    if not placed: out.append([s, e])
    return out
```

```cpp
// C++17 (from the PDF)
vector<vector<int>> insert(vector<vector<int>>& a, vector<int>& b){
    int s=b[0], e=b[1]; vector<vector<int>> out; bool placed=false;
    for (auto &x : a){
        if (x[1] < s) out.push_back(x);
        else if (e < x[0]){ if(!placed){ out.push_back({s,e}); placed=true; } out.push_back(x); }
        else { s=min(s,x[0]); e=max(e,x[1]); }
    }
    if(!placed) out.push_back({s,e});
    return out;
}
```
</details>

---

<details>
<summary><strong>📄 LCCF Problem Sheet — Codeforces “Two Doors”</strong> (snippet)</summary>

**Saved as:** `LCCF/out_pdfs/problems/problem-sheet-cf-2-doors.pdf`

```tex
\section*{Problem: Two Doors (CF)}
\paragraph{ELI5.} Pick the nearer door by absolute difference.
\paragraph{Solution.} For $a,b,c$, compute $d_1=|a-c|$, $d_2=|b-c|$ and output
$1$ if $d_1<d_2$, $2$ if $d_2<d_1$, else $3$.
\paragraph{Complexity.} \mathcal{O}(1) per test.
```

```python
def solve():
    import sys
    it = iter(sys.stdin.read().split()); t = int(next(it))
    ans = []
    for _ in range(t):
        a,b,c = map(int, (next(it), next(it), next(it)))
        d1, d2 = abs(a-c), abs(b-c)
        ans.append("1" if d1<d2 else "2" if d2<d1 else "3")
    print("\n".join(ans))
```
</details>

---

<details>
<summary><strong>📘 Study Sheet — “LU and QR Factorizations”</strong> (snippet)</summary>

**Saved as:** `Study Sheet Generator/out_pdfs/topics/study-sheet-11-lu-and-qr-factorizations.pdf`

```tex
\section*{Study Sheet 11 — LU and QR Factorizations}

\paragraph{Overview.}
$A=LU$ (with pivoting) and $A=QR$ with $Q^\top Q=I$ and $R$ upper-triangular.

\paragraph{Formula Canon.}
Least-squares solution via QR: $x = R^{-1}Q^\top b$, and
\mathrm{proj}_{\mathrm{col}(A)}(b)=QQ^\top b$.

\paragraph{Worked Example (Classical Gram–Schmidt).}
$q_1=a_1/\|a_1\|$, $r_{12}=q_1^\top a_2$, $u_2=a_2-r_{12}q_1$, 
$q_2=u_2/\|u_2\|$, $R=\begin{bmatrix}\|a_1\|&r_{12}\\0&\|u_2\|\end{bmatrix}$.
```

```python
# From-scratch QR (snippet from the PDF)
import numpy as np
def qr_gs(A):
    A = np.array(A, float); m,n = A.shape
    Q, R = np.zeros((m,n)), np.zeros((n,n))
    for j in range(n):
        v = A[:, j].copy()
        for i in range(j):
            R[i,j] = Q[:,i] @ v
            v -= R[i,j] * Q[:,i]
        R[j,j] = np.linalg.norm(v); Q[:,j] = v / R[j,j]
    return Q, R
```
</details>

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

