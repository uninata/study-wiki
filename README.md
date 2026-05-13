# LLM Wiki — CTU Coursework Knowledge Base

A personal, AI-maintained knowledge base for coursework at the Czech Technical University in Prague (CVUT), built on [Andrej Karpathy's LLM Wiki pattern](https://karpathy.ai/blog/wiki.html).

## What is an LLM Wiki?

An LLM Wiki is a structured knowledge base where an LLM (in this case Claude) acts as the librarian. The human curates source materials and asks questions; the LLM reads, summarizes, cross-links, and maintains the wiki. Knowledge compounds over time — answers and insights are filed into interlinked pages rather than lost in chat history.

The architecture has three layers:

| Layer | Purpose | Location |
|-------|---------|----------|
| **Raw sources** | Immutable lecture PDFs, notebooks, slides | `raw/` (git-ignored — not in this repo) |
| **Wiki pages** | Markdown summaries with LaTeX math, citations, and wikilinks | `wiki/` |
| **Schema** | Rules governing how the LLM maintains the wiki | `CLAUDE.md` |

The LLM follows `CLAUDE.md` as its instruction set — it defines the page format, ingest workflow, citation rules, mathematical notation standards, and cross-linking conventions.

## Courses

| Folder | Course | Status |
|--------|--------|--------|
| `ml-fundamentals/` | Machine Learning Fundamentals | ✅ Active |
| `ml-methods/` | Machine Learning Methods | ✅ Active |
| `computer-vision/` | Computer Vision | ✅ Active |
| `pattern-recognition/` | Pattern Recognition and Machine Learning | ✅ Active |
| `deep-learning/` | Deep Learning | ✅ Active |
| `nlp/` | Natural Language Processing | ✅ Active |
| `programming-for-engineers/` | Programming for Engineers | ✅ Active |
| `cybersecurity/` | Cybersecurity | ✅ Active |
| `stai/` | Selected Topics in AI | 🚫 Empty — no exam this semester |
| `shared/` | Cross-course concepts (SVMs, transformers, etc.) | ✅ Active |

## How to set up in Obsidian

This wiki is designed to be browsed in [Obsidian](https://obsidian.md), a free Markdown editor with native support for `[[wikilinks]]` and graph visualization.

### Steps

1. **Clone this repo**:
   ```bash
   git clone https://github.com/<your-username>/llm-wiki.git
   ```

2. **Open as an Obsidian vault**: Open Obsidian → "Open folder as vault" → select the `llm-wiki/` directory.

3. **Start browsing**: Open `wiki/index.md` as your entry point. All `[[wikilinks]]` are clickable and will navigate between pages.

### Recommended Obsidian settings

- **Files & Links → New link format**: set to "Relative path to file" so wikilinks resolve correctly across subdirectories
- **Files & Links → Use [[Wikilinks]]**: enable (should be on by default)
- **Community plugins → Dataview** (optional): enables querying pages by metadata fields (Course, Sources, Last updated)
- **Graph view**: use it to visualize how concepts connect across courses — shared pages will appear as high-degree hub nodes

### What you'll see

- **`wiki/index.md`** — master table of contents for all courses
- **`wiki/log.md`** — append-only changelog of all wiki operations
- **`wiki/<course>/`** — course-specific concept pages
- **`wiki/shared/`** — cross-course pages (e.g., SVMs appear in ML Fundamentals, Pattern Recognition, and ML Methods)
- **`CLAUDE.md`** — the schema file that governs how the LLM maintains everything

### LaTeX rendering

Pages contain LaTeX math (`$inline$` and `$$display$$`). Obsidian renders these natively — no plugins needed.

## Where are the source materials?

The `raw/` folder containing lecture PDFs, seminar solutions, and notebooks is **not included in this repository** — these are copyrighted course materials available on [CourseWare](https://cw.fel.cvut.cz/). The wiki pages cite their sources by filename (e.g., `(source: ml-fundamentals/ls26_lecture_erm.pdf)`) so you can trace any claim back to the original document.

If you want to use the ingest workflow yourself, create the `raw/` folder manually with per-course subdirectories and place the PDFs there:

```bash
mkdir -p raw/{ml-fundamentals,ml-methods,computer-vision,stai,nlp,deep-learning,cybersecurity,programming-for-engineers,pattern-recognition}
# Then download the relevant PDFs from CourseWare into each folder
```

## How it's maintained

All wiki operations are performed by Claude Code following the `CLAUDE.md` schema:

- **Ingest**: read a new source → discuss takeaways → create/update wiki pages → cross-link → update index and log
- **Query**: answer questions by reading relevant wiki pages, citing sources
- **Lint**: audit for broken links, missing cross-references, stale content, formatting issues

Every change is logged in `wiki/log.md` with a structured entry: `## [YYYY-MM-DD] operation | source | course`.
