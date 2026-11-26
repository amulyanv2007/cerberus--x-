# CERBERUS-X

**A knowledge system where every modality can contradict the others.**
Multimodal Evidence Graph Construction & Conflict-Aware Reasoning

Domain: AI / ML / GenAI

---

## What is it?

CERBERUS-X is an **offline** system that reads clues from **documents, tables, scanned records and images**, finds the clues that **disagree**, scores how much each source can be **trusted**, and answers questions **with proof**. When the evidence is weak, it says **"not enough evidence"** instead of guessing.

Think of it as a robot detective:

1. **Read** every clue (text, tables, scans, pictures).
2. **Compare** the clues and find the ones that fight each other.
3. **Answer** using the most trusted evidence, and show where it came from.

## Why do we need it?

- Real information comes from many places, and those places often disagree.
- Some documents are fake, edited or contain hidden instructions.
- Normal tools pick one answer and sound sure, even when they are wrong.
- CERBERUS-X shows its sources, its conflicts and its confidence.

## How is it different?

| Most simple tools | CERBERUS-X |
|---|---|
| Treat every source as equally true | Gives each source a **trust score** |
| Do not notice when files disagree | **Detects and shows conflicts** |
| Mostly read text | Reads **text, tables, scans and images together** |
| Can be tricked by "ignore all rules" inside a file | Treats files as **evidence, never as commands** |
| Always answer, even with a guess | Can say **"not enough evidence"** |
| Need internet | Runs **fully offline** |

---

## System architecture

```
User uploads files + asks a question
        |
1. Input Processing        (accept PDF, image, CSV/Excel, Word, scans)
        |
2. Data Extraction         (OCR, PDF text, table parsing)
        |
3. Data Normalization      (clean data, match names, standard dates/times)
        |
4. Knowledge Graph         (people, places, documents and their links)
        |
5. Conflict Detection      (same entity + same fact + different value)
        |
6. Source Reliability &    (trust scores, tamper and trick checks)
   Reasoning Engine
        |
7. Answer Generation       (answer / answer with warning / not enough evidence)
        |
8. User Dashboard          (answer, sources, graph, confidence)
```

---

## Tools and technologies

Status key: **Done** = already working in this repo, **Planned** = part of the roadmap, not built yet.

### Core language and data

| Tool | What it does in our project | Status |
|---|---|---|
| **Python 3** | Main programming language for everything | Done |
| **pandas** | Reads CSV/Excel tables and builds the claims table | Done |
| **openpyxl** | Reads and writes Excel files | Planned |

### Reading files (ingestion)

| Tool | What it does | Status |
|---|---|---|
| **Tesseract OCR** + **pytesseract** | Reads words from scanned images (the "robot eyes") | Done |
| **Pillow (PIL)** | Opens and prepares images for OCR | Done |
| **PyMuPDF / pdfplumber** | Reads text and tables from PDF files | Planned |
| **python-docx** | Reads Word (.docx) files | Planned |
| **EasyOCR / PaddleOCR** | Backup OCR options for harder scans | Planned |

### Understanding and matching

| Tool | What it does | Status |
|---|---|---|
| **Rule-based extraction (regex + word lists)** | Finds who, where and when in sentences | Done (simple version) |
| **Name matching** | Treats "Tom Sharma", "T. Sharma" and "Tommy S." as one person | Done (simple version) |
| **RapidFuzz** | Smarter fuzzy name matching | Planned |
| **sentence-transformers** | Meaning-based matching and search | Planned |
| **NLI model (e.g. DeBERTa-MNLI)** | Decides if two claims agree, disagree or are unrelated | Planned |

### Knowledge graph and search

| Tool | What it does | Status |
|---|---|---|
| **NetworkX** | Builds the evidence graph (dots and lines) | Done |
| **matplotlib** | Draws the clue map picture, with a red conflict line | Done |
| **PyVis / Plotly** | Interactive graph inside the website | Planned |
| **SQLite** | Stores claims, sources and trust scores | Planned |
| **FAISS / ChromaDB** | Offline vector search over documents | Planned |

### Trust, tamper and trick checks

| Tool | What it does | Status |
|---|---|---|
| **Source trust scores** | Gives each source a score (official > unsigned note) | Done (hand-set values) |
| **Prompt-injection check** | Catches "ignore all previous instructions" inside files | Done (simple version) |
| **Outlier check** | Flags weird values such as 9999999 in a table | Done (simple version) |
| **Image metadata / error-level checks** | Spots edited or tampered pictures | Planned |

### Answering and website

| Tool | What it does | Status |
|---|---|---|
| **Ollama + a local LLM (Llama / Mistral)** | Writes the final answer in plain words, offline | Planned |
| **Streamlit** | The website: upload files, ask questions, see answers and graph | Planned |
| **Flask / FastAPI** | Optional back-end if we split the website from the engine | Planned |

---

## Folder structure

```
cerberus_x_final/
├── docs/                 text documents (police report, diary note, fake note)
├── tables/               attendance.csv
├── scans/                shop_receipt.png (+ saved OCR text as a backup)
├── output/               claims.csv and clue_map.png are created here
├── ingest_simple.py      reads all files and builds the claims table + conflict check
├── clue_map.py           draws the evidence graph (clue map)
├── requirements.txt      Python packages needed
├── RUN_ME.bat            double-click to run everything on Windows
└── README.md
```

## How to run (current version)

1. Install **Python 3** (tick "Add python.exe to PATH" during install).
2. Optional but recommended: install **Tesseract OCR** (Windows: UB-Mannheim build) so scans are read live. Without it, the code uses a saved text copy of the receipt and says so in the output.
3. Install packages:

```
py -m pip install -r requirements.txt
```

4. Build the claims table and conflict check:

```
py ingest_simple.py
```

5. Draw the clue map (saved to `output/clue_map.png`):

```
py clue_map.py
```

Or just double-click **RUN_ME.bat**.

## Demo story (sample data)

Question: **"Where was Tom Sharma around 5 PM?"**

| Source | Type | Says |
|---|---|---|
| Police report | Text | home |
| Attendance table (2 rows) | Table | home |
| Diary note | Text | shop (unsure) |
| Shop receipt | Scan | shop |
| Anonymous note | Text | contains a hidden command, flagged and ignored |

Result: **home scores 2.65, shop scores 1.00**, so the system reports a conflict and picks **home**. The fake note and the weird table value are ignored and listed as such.

## Roadmap

| Step | Goal | Status |
|---|---|---|
| 1 | Understand the problem | Done |
| 2 | Plan the solution | Done |
| 3 | Prepare data (text, table, scan, planted conflicts) | Done (small sample) |
| 4 | Core features: read text, tables, scans | Done (simple version) |
| 5 | Evidence graph and clue map | Done |
| 6 | Answerer: answer / conflict warning / "not enough evidence" | Next |
| 7 | Website (Streamlit) with graph and sources | Planned |
| 8 | Tamper checks, smarter AI extraction, testing and polish | Planned |

## Known limits (honest notes)

- Claim extraction uses **simple rules**, not an AI model yet.
- Trust scores are **hand-set**, not learned.
- The graph ignores the school row because it is about a different time of day. That rule is written into the code, not detected automatically yet.
- The sample dataset is small and made by us to test conflicts, fakes and outliers.

## Key focus

Accuracy and reliability, conflict resolution, and traceability (every answer shows its sources).
