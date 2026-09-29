# Maintenance Handover

Test releases of an Obsidian plugin for **maintenance handovers**.

Use it when someone who knows a software system is leaving, and the knowledge needed to maintain it
is scattered across notes, documents, spreadsheets and their own head. It gathers that material,
extracts an evidence-backed model of the system, shows what is still missing, and produces a
handover the next maintainer can use.

This repo holds **releases only**. The source is not public.

## How it works

```mermaid
flowchart LR
    A["Your material<br/>notes, PDF, DOCX, XLSX,<br/>PPTX, CSV, pasted text"] --> B["Index<br/>searchable, local"]
    B --> C["Memory map<br/>typed entities, each claim<br/>with source + quote"]
    C --> D["Coverage and gaps<br/>scored by code against<br/>a handover checklist"]
    D --> E["Handover report<br/>every statement linked<br/>to its evidence"]
    D -. "gap questions" .-> F(["Outgoing developer"])
    F -. "answers, new notes" .-> A
    B --> G["Grounded chat"]
    C --> G
```

**Chat is not the point.** It is one way to explore the material. What makes this a handover tool
is the memory map and the coverage analysis: they turn a pile of notes into a model of the system
and a list of what is still unknown, which a search box over your vault can't do.

### An example

```
Your vault contains:                       Memory map (linked notes, every claim
  deployment-notes.md                      cites its source and a verbatim quote):
  architecture.pdf
  incident-2025-03.docx                      Services      API · PostgreSQL · Import worker
  customer-import.xlsx                       Environments  production · staging
  runbook.md                                 People        Alice (database owner) · Bob (deploys)
                                             Procedures    deploy API · restore database
                     ↓
              Coverage against the handover checklist     Handover report
                                                          (built from the map, each statement
  ✓ Architecture                                           linked to its evidence, with the gaps
  ✓ Deployment                                             listed as questions to answer)
  ✓ Production environment
  ✗ Database recovery procedure    → "Who has restored production, and how?"
  ✗ Third-party credentials        → "Where are they kept?"
  ✗ Incident escalation            → "Who is called first, and when?"
```

The gaps are computed by code against a fixed checklist, not by the model, so the report shows
what your material leaves out rather than what the model chose to mention. The outgoing
developer answers the questions; the map and report update.

<details>
<summary><b>What happens inside the plugin</b> (indexing, map building, chat)</summary>

Everything below runs inside Obsidian against one embedded SQLite file. There is no server and no
second process. The only outside calls are to your chat and embedding endpoints.

**1. Indexing.** Notes and imported documents become searchable chunks.

```mermaid
flowchart LR
    N["Note saved in vault"] --> C["Split into chunks<br/>(title added to each)"]
    S["Imported document<br/>PDF, DOCX, XLSX, PPTX, CSV"] --> T["Extract text"] --> C
    C --> E["Embedding model<br/>(your server)"]
    E --> DB[("SQLite<br/>text + vectors")]
```

Unchanged notes are never re-embedded. Imported documents live only in the database; the original
file is not copied into your vault.

**2. Building the memory map.** The model reads; code decides.

```mermaid
flowchart TD
    A["Each note or document"] --> M["Chat model, one call per artifact<br/>proposes entities, relations, quotes"]
    M --> K[("Checkpoint<br/>a failed build resumes")]
    K --> V{"Code checks"}
    V -->|"quote not found in source"| X["Rejected"]
    V -->|"quote verified"| I["Merge duplicate names,<br/>pick newest value,<br/>flag conflicts"]
    I --> MAP[("Memory map")]
    MAP --> G["Coverage and gaps<br/>vs. handover checklist"]
    MAP --> N["Linked notes in your vault"]
    G --> R["Handover report<br/>no model call"]
```

Only the first step uses a model. Verification, merging, conflicts, coverage and the report are
plain code, so the report shows what your material leaves out, not what the model chose to mention,
and re-running them costs no model calls.

**3. Answering in chat.**

```mermaid
flowchart TD
    Q["Your question"] --> P["Plan: rewrite as standalone<br/>search queries"]
    P --> H["Hybrid search<br/>meaning (vectors) + exact words,<br/>glossary terms expanded"]
    P --> W["Memory-map lookup<br/>entities named in the question,<br/>plus their neighbours"]
    H --> X["Context, newest first,<br/>secrets masked"]
    W --> X
    X --> L["Chat model<br/>answers only from context"]
    L --> Y["Streamed answer<br/>with dated [n] citations"]
```

</details>

## What it does

- **Artifacts in:** your notes, plus PDF, Word (DOCX), Excel (XLSX), PowerPoint (PPTX), CSV and
  text files, and pasted text about the project.
- **Memory map:** each artifact is classified into a typed map of the system (environments,
  services, people, procedures…). Every claim keeps its source and a verbatim quote, and the
  map is rendered as linked notes in your vault.
- **Coverage and gaps:** the map is scored against a handover checklist by code. Each missing area
  becomes a gap with a question for the outgoing developer.
- **Handover report:** a report built from the map, with each statement linked to its evidence.
- **Grounded chat:** a streamed assistant pane that answers from your material and cites every
  source, dated, so conflicting notes resolve by recency.

**Your project data stays within the systems you configure.** The plugin has no cloud backend. Its
database lives in the vault's plugin folder, and the only network calls go to the
OpenAI-compatible chat and embedding endpoints you set up. Those endpoints receive your content,
so use a local server for confidential material (see *Secrets and what the model sees*).

## Requirements

- **Obsidian desktop** (Windows, macOS, Linux), 1.5.0 or later. Mobile is not supported.
- **A local model server** with an OpenAI-compatible API, e.g. [llama.cpp](https://llama.app/), [LM Studio](https://lmstudio.ai)
  or [Ollama](https://ollama.com), serving:
  - a **chat model** (an instruction-tuned model; larger models classify noticeably better), and
  - an **embedding model** (e.g. `nomic-embed-text`).

  Any OpenAI-compatible endpoint works, but local is the point.

## Install

### With BRAT (recommended, installs updates for you)

1. In Obsidian: *Settings → Community plugins → Browse*, install and enable
   **BRAT** ("Obsidian42 - BRAT").
2. Run *BRAT: Add a beta plugin for testing* and enter:

   ```
   https://github.com/pluptak/maintenance-handover
   ```

3. Enable **Maintenance Handover** under *Settings → Community plugins*.

BRAT checks for new releases on startup and updates the plugin.

### By hand

1. Open the [latest release](https://github.com/pluptak/maintenance-handover/releases/latest)
   and download `main.js`, `manifest.json` and `styles.css`.
2. Put them in `<your vault>/.obsidian/plugins/maintenance-handover/` (create the folder).
3. Restart Obsidian (or reload it) and enable **Maintenance Handover** under
   *Settings → Community plugins*.

To update, replace the three files. Your database (`kb.sqlite`) and settings (`data.json`) in
that folder are kept.

## First steps

1. **Point it at your model server** in the plugin's settings. For LM Studio:
   - Chat provider and embedding provider: `http://localhost:1234/v1` (Ollama:
     `http://localhost:11434/v1`)
   - Chat model and embedding model: ids **your server actually serves**, as it lists them.
   - API key: leave empty for a local server.
2. **Set up the vault.** Run *Knowledge base: Set up this vault* from the command palette. It
   explains what it changes, then creates the vault's notebook and indexes your notes with the
   embedding model, which is why the server comes first. Use one vault per project.
3. **Run *Diagnostics: Health check*.** Every row should be green or explain itself. This is
   the first thing to include in a bug report.
4. **Try it:**
   - open the assistant from the ribbon and ask about your project;
   - add a document with *Capture: Add document source* (PDF, DOCX, XLSX, PPTX, CSV or TXT),
     or selected text with *Capture: Add selection as source*;
   - run *Memory map: Build*, then *Report: Handover (from memory map)*.

> Changed the embedding model? Run *Index: Rebuild (re-embed everything)*. Without it, existing
> notes keep old vectors and silently drop out of search.

## Secrets and what the model sees

- **Your model server sees your content as written.** Indexing sends every note and source to the
  embedding model, and *Memory map: Build* sends each whole note or source to the chat model,
  unredacted. Use a server you trust with everything in the vault. For confidential projects that
  means a local server, not a hosted API.
- **What the plugin writes is redacted.** Values that look like secrets are replaced with
  `[REDACTED]` in the memory map, the handover reports, chat answers and notes saved from chat.
  That covers `password: …` and `token=…` assignments, passwords stated in a sentence
  ("the password is now X"), AWS keys, JWTs, bearer tokens, private-key blocks and the password in
  a `scheme://user:pass@host` address. Chat also masks the retrieved text before the model sees it.
  What you type into chat is sent as you wrote it.
- **Redaction is a safety net, not a guarantee.** It's deliberately narrow so it doesn't mangle
  ordinary text, so a secret with no label and no known shape gets through. Keep credentials in a
  password manager and write down only where they are, such as a KeePass entry path. The plugin
  keeps those paths readable on purpose.
- **Credentials are for a person to fill in.** The report's *Credential References* and
  *Access Checklist* sections are never written by the model. They're left for the outgoing
  maintainer.

## Known limitations

- Desktop only.
- Scanned (image-only) PDFs have no text layer, and there is no OCR.
- A very large document briefly freezes the UI while it is read.
- CSV and TXT files must be saved as UTF-8 (in Excel: Save As → "CSV UTF-8"); other encodings are
  refused with a message saying so.
- Tables are cut to 2,000 rows and 64 columns per sheet or CSV. Excel dates come in as numbers
  (such as 45292), not as dates yet.
- The memory map skips any note or document over 20,000 characters (roughly ten pages). It stays
  searchable in chat; split it to get it into the map.
- Editing a note while the model is answering stops that answer.
- This is a **test release**: expect rough edges, and keep a backup of any vault you care about.
  The plugin adds `on-*` fields (an id and a content hash) to your notes' frontmatter. Everything
  else it writes goes into its own generated notes.

## Reporting problems

Open an [issue](https://github.com/pluptak/maintenance-handover/issues) with:

- the plugin version and build line from the top of the plugin's settings (or the health check);
- the *Diagnostics: Health check* output;
- your model server and model ids;
- what you did, what you expected, and what happened, plus any errors from the developer
  console (*Ctrl+Shift+I* / *Cmd+Option+I* → Console).

**Don't paste confidential project content into issues.** They are public.

## License

[MIT](LICENSE)
