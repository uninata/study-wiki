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

## Source Materials

The `raw/` folder containing lecture PDFs, seminar solutions, and notebooks is **included in this repository**. Wiki pages cite their sources by filename (e.g., `(source: ml-fundamentals/ls26_lecture_erm.pdf)`) so you can trace any claim back to the original document.

## Connecting an LLM

The wiki is designed to be maintained by an LLM that follows an instruction file as its system prompt. `CLAUDE.md` is used by Claude Code; `AGENTS.md` is a model-agnostic version for use with other LLMs (e.g., GPT-4, Gemini, local models). There are several ways to connect one:

### Option 1: Claude Code (CLI)

The original setup. [Claude Code](https://docs.anthropic.com/en/docs/claude-code) automatically reads `CLAUDE.md` when you open the project directory:

```bash
cd llm-wiki
claude
```

Then just ask it to ingest, query, or lint — it knows the workflows.

### Option 2: Obsidian Copilot Plugin

[Obsidian Copilot](https://github.com/logancyang/obsidian-copilot) lets you chat with an LLM directly inside Obsidian, with access to your vault contents.

**Setup**:
1. Install the **Copilot** community plugin in Obsidian (Settings → Community plugins → Browse → "Copilot")
2. Configure a model provider (see local/remote options below)
3. Paste the contents of `CLAUDE.md` into the plugin's **system prompt** field (Settings → Copilot → System Prompt) so the LLM follows the wiki conventions
4. Chat with the LLM in Obsidian's side panel — it can read and reference your wiki pages

### Option 3: Any LLM with file access

Any LLM tool that can read local files works. Just make sure it has access to `CLAUDE.md` as its system instructions.

### Local vs Remote LLM

| | Local | Remote |
|--|-------|--------|
| **What** | Model runs on your machine | Model runs on a provider's servers |
| **Examples** | Ollama, LM Studio, llama.cpp | OpenAI API, Anthropic API, Google AI |
| **Privacy** | Data never leaves your machine | Data sent to provider |
| **Hardware** | Needs GPU (8GB+ VRAM for good models) | Just needs internet |
| **Quality** | Smaller models, may struggle with LaTeX/math | Best models available |

**Setting up a local model (Ollama)**:
1. Install [Ollama](https://ollama.ai): `brew install ollama`
2. Pull a model: `ollama pull llama3.1` (or `mistral`, `qwen2.5`, etc.)
3. Ollama runs a local API at `http://localhost:11434`
4. Point your Obsidian Copilot or other tool to this endpoint

**Setting up a remote model**:
1. Get an API key from your provider ([Anthropic](https://console.anthropic.com/), [OpenAI](https://platform.openai.com/), etc.)
2. Enter the API key in your tool's settings (e.g., Obsidian Copilot → Model Provider)

**Recommendation**: For wiki maintenance (ingest, cross-linking, LaTeX math), use a strong remote model (Claude, GPT-4). For simple queries over existing pages, a local model works fine.

## How it's maintained

All wiki operations are performed by Claude Code following the `CLAUDE.md` schema:

- **Ingest**: read a new source → discuss takeaways → create/update wiki pages → cross-link → update index and log
- **Query**: answer questions by reading relevant wiki pages, citing sources
- **Lint**: audit for broken links, missing cross-references, stale content, formatting issues

Every change is logged in `wiki/log.md` with a structured entry: `## [YYYY-MM-DD] operation | source | course`.
