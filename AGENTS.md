# LLM Wiki

A personal knowledge base for CVUT coursework, maintained by Codex.
Based on Andrej Karpathy's LLM Wiki pattern.

## Purpose

This wiki is a structured, interlinked knowledge base spanning multiple courses at CVUT.
Codex maintains the wiki. The human curates sources, asks questions, and guides the analysis.
Knowledge compounds over time — answers and insights are filed back into the wiki rather than lost in chat history.

## Courses

| Slug | Course |
|------|--------|
| `ml-fundamentals` | Machine Learning Fundamentals |
| `ml-methods` | Machine Learning Methods |
| `computer-vision` | Computer Vision |
| `stai` | Selected Topics in AI |
| `nlp` | Natural Language Processing |
| `deep-learning` | Deep Learning |
| `cybersecurity` | Cybersecurity |
| `programming-for-engineers` | Programming for Engineers |
| `pattern-recognition` | Pattern Recognition and Machine Learning |

## Folder structure

```
raw/                            -- source documents (immutable — NEVER modify these)
  <course-slug>/                -- one subfolder per course
wiki/                           -- markdown pages maintained by Codex
  index.md                      -- master table of contents for the entire wiki
  log.md                        -- append-only record of all operations
  <course-slug>/                -- course-specific wiki pages
  shared/                       -- cross-course concept pages (e.g., svm.md, gradient-descent.md)
templates/
  page-template.md              -- standard page skeleton
```

## Ingest workflow

When the user adds a new source to `raw/<course>/` and asks you to ingest it:

1. Read the full source document (PDF, notebook, .py file, or other format)
2. Discuss key takeaways with the user before writing anything
3. Create a summary page in `wiki/<course>/` named after the source
4. Create or update concept pages for each major idea or entity
   - If a concept is specific to one course, place it in `wiki/<course>/`
   - If a concept spans multiple courses, place it in `wiki/shared/`
5. Add wiki-links (`[[page-name]]`) to connect related pages — use `[[course-slug/page-name]]` for cross-course links
6. Update `wiki/index.md` with new pages and one-line descriptions
7. Append an entry to `wiki/log.md` (see log format below)

A single source may touch 10–15 wiki pages. That is normal.

## Page format

Every wiki page must follow this structure:

```markdown
# Page Title

**Summary**: One to two sentences describing this page.

**Course**: Which course(s) this page relates to.

**Sources**: List of raw source files this page draws from.

**Last updated**: Date of most recent update.

---

Main content goes here. Use clear headings and short paragraphs.

Link to related concepts using [[wiki-links]] throughout the text.

## Related pages

- [[related-concept-1]]
- [[related-concept-2]]
```

## Log format

Every operation appends to `wiki/log.md` using this structured format:

```
## [YYYY-MM-DD] operation | source-name | course-slug

Brief description of what changed (pages created, updated, or linked).
```

Operations: `ingest`, `query`, `lint`, `update`, `reorganize`.

## Mathematical Notation Standards

All mathematical expressions must use **LaTeX format** for consistency and visual clarity:

**Display math** (equations on their own lines):
```markdown
$$E(x) = \frac{1}{n} \sum_{i=1}^{n} x_i$$
```

**Inline math** (within text):
```markdown
The mean $\mu = \frac{1}{n}\sum_i x_i$ is computed as...
```

**Notation conventions:**
- Vectors/matrices: `\mathbf{w}, \mathbf{X}` (bold)
- Sets: `\mathcal{H}, \mathcal{X}` (calligraphic)
- Domains: `\mathbb{R}^d, \mathbb{N}` (blackboard bold)
- Operators: `\arg\max, \arg\min, \sum, \prod, \mathbb{E}` (function names)
- Greek letters: `\alpha, \beta, \theta, \sigma, \pi` (backslash-prefixed)
- Symbols: `\approx, \leq, \geq, \neq, \to, \times, \infty` (spelled out)

**Examples:**
- Wrong: `h*(x) = argmax_y p(y|x)` 
- Right: `$$h^*(x) = \arg\max_y p(y|x)$$`

- Wrong: `KL(p || q) >= 0`
- Right: `$$D_{\text{KL}}(p \| q) \geq 0$$`

---

## Citation rules

- Every factual claim should reference its source file
- Use the format `(source: course-slug/filename.pdf)` after the claim
- If two sources disagree, note the contradiction explicitly
- If a claim has no source, mark it with `[needs verification]`

## Cross-course linking

Many concepts appear across courses (e.g., SVMs in ML_Fundamentals and ML_Methods, backpropagation in Deep Learning and ML_Methods). When this happens:

- Create one canonical page in `wiki/shared/`
- Each course page that touches the concept should link to the shared page
- The shared page should list all courses where the concept appears
- Course-specific details (e.g., a specific homework formulation) stay in the course page

## Question answering

When the user asks a question:

1. Read `wiki/index.md` first to find relevant pages
2. Read those pages and synthesize an answer
3. Cite specific wiki pages in your response
4. If the answer is not in the wiki, say so clearly
5. If the answer is valuable, offer to save it as a new wiki page

## Lint

When the user asks you to lint or audit the wiki:

- Check for contradictions between pages
- Find orphan pages (no inbound links from other pages)
- Identify concepts mentioned in pages that lack their own page
- Flag claims that may be outdated based on newer sources
- Check that all pages follow the page format above
- Look for duplicate concept pages that should be merged into `shared/`
- Report findings as a numbered list with suggested fixes

## Rules

- Never modify anything in the `raw/` folder
- Always update `wiki/index.md` and `wiki/log.md` after changes
- Keep page names lowercase with hyphens (e.g., `gradient-descent.md`)
- Write in clear, plain language
- **All mathematical expressions must use LaTeX format** (see [[Mathematical Notation Standards](#mathematical-notation-standards)) — no plain-text math like `h*(x) = argmax_y p(y|x)`
- When uncertain about how to categorize something, ask the user
- Obsidian compatibility: use `[[wikilinks]]` syntax for all internal links
