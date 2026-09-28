# Maintenance Handover

Test releases of an Obsidian plugin for **maintenance handovers**. It turns a vault of project notes
and documents into a local knowledge base, maps what is known about the project, and shows what
is missing before the new maintainer needs it.

This repo holds **releases only**. The source is not public.

## What it does

- **Artifacts in:** your notes, plus PDF, Word (DOCX), Excel (XLSX), PowerPoint (PPTX), CSV and
  text files, and pasted text about the project.
- **Grounded chat:** a streamed assistant pane that answers from your material and cites every
  source, dated, so conflicting notes resolve by recency.
- **Memory map:** each artifact is classified into a typed map of the system (environments,
  services, people, procedures…). Every claim keeps its source and a verbatim quote, and the
  map is rendered as linked notes in your vault.
- **Coverage and gaps:** the map is scored against a handover checklist by code. Each missing area
  becomes a gap with a question for the outgoing developer.
- **Handover report:** a report built from the map, with each statement linked to its evidence.

**Everything stays on your machine.** The plugin's database lives in the vault's plugin folder,
and the only network calls go to the model server you configure.

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
