## How It Works

### LCCF — Problem Sheet Generator

**What I feed it (CSV, fields auto-detected):**
- `platform/site/source` (e.g., `cf` or `lc`)
- `title`, `statement_text/statement/description`, `url`
- For LC: `id/slug/frontend_id`
- For CF: `contestId/contest_id`, `index/problem_index`
- Optional: `tags`, `rating`, `difficulty`, `paid_only`

**What it spits out (per problem):**
- `LCCF/out_pdfs/problems/problem-sheet-<platform>-<title>.tex`
- `LCCF/out_pdfs/problems/problem-sheet-<platform>-<title>.pdf`

---

### Study Sheet Generator

**What I feed it:**
- `topics.csv` (one topic per row)

**What it spits out (per topic):**
- `Study Sheet Generator/out_pdfs/topics/study-sheet-<n>-<topic>.tex`
- `Study Sheet Generator/out_pdfs/topics/study-sheet-<n>-<topic>.pdf`

---

**Note:** I also add three blank pages after every generated PDF for handwritten notes, sketches, and quick derivations.
