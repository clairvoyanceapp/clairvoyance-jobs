# Job Types

Job types power the **Jobs** dashboard tile. Each JSON file here is a bundled default,
seeded into the user's `config/jobs/` directory on startup (missing files are copied;
bundled files with a higher `version` refresh the stored copy's bundled-owned keys).

**Users and third parties add their own job types by dropping a `.json` file into
`{userData}/config/jobs/`** — no code changes needed. The Jobs tile reads that
directory at runtime.

## File format

```json
{
  "id": "my-job",
  "version": 1,
  "name": "My Job",
  "workflow": "exhibit",
  "icon": "clipboard-list",
  "description": "One line shown as the pad tooltip and in the create dialog.",
  "namePlaceholder": "e.g., Example Exhibit Name",
  "briefPlaceholder": "Placeholder text for the brief textarea...",
  "promptScaffold": "Instructions prepended to the user's brief when the exhibit AI runs...",
  "promptReference": "optional-protocol.md",
  "source": {
    "label": "What do you want audited?",
    "options": [
      { "id": "codebase", "label": "Codebase", "kind": "folder",
        "prompt": "The subject to audit is the codebase in this folder: {value}" },
      { "id": "website", "label": "Website", "kind": "url", "placeholder": "https://..." },
      { "id": "other", "label": "Other", "kind": "instructions" }
    ]
  }
}
```

| Field | Required | Notes |
|-------|----------|-------|
| `id` | yes | Stable unique id, lowercase kebab-case. Custom jobs should use a vendor prefix (e.g. `acme-roi-model`) to avoid colliding with bundled ids. |
| `version` | no | Integer. Only meaningful for bundled defaults — a higher bundled version refreshes the seeded copy. Custom files can omit it. |
| `name` | yes | Display name on the pad and in the "New {name}" dialog title. |
| `workflow` | no | Execution path: `exhibit` (default), `translation`, `computer-setup`, `second-brain`, `folder-organizer`, or `local-job`. Specialized workflows produce files or perform operational work rather than creating an Exhibit. `local-job` runs a host-orchestrated pipeline on a local model; set `localJobKind` to the job kind id (`summarize-file`, `classify-folder`, `web-report`, `data-exhibit`) and the tile hides the pad until that kind is registered. |
| `icon` | no | Lucide icon name in kebab-case (e.g. `layout-dashboard`). Must exist in `src/utils/iconResolver.ts` ICON_MAP; unknown names fall back to a default icon. |
| `description` | no | One-liner shown as tooltip/subtitle. |
| `namePlaceholder` | no | Placeholder for the optional job-name field. |
| `briefPlaceholder` | no | Placeholder for the brief textarea. |
| `promptScaffold` | yes | The canned instructions that define the job. Prepended to the user's input when starting the selected workflow. Keep it directive and include the workflow's correctness and safety rules. |
| `promptReference` | no | Safe local `.md` filename beside the job JSON. Its full contents are appended to `promptScaffold`, ensuring the staff receives a bundled protocol rather than merely being told to locate it. Bundled reference documents are seeded with the default jobs. |
| `source` | no | Structured data-source chooser (see below). Omit it and the dialog shows a plain free-form Brief textarea. |

## The `source` block

Most jobs act on *something* — a file, a folder, a URL. The `source` block turns the
dialog's input into a two-level chooser: a combo (`label`, e.g. "What do you want
audited?") whose selected option reveals the matching input control.

Each option needs `id`, `label`, and a `kind`:

| `kind` | Second-level UI | Prompt contribution |
|--------|-----------------|---------------------|
| `file` | "Choose File..." button (native picker) | `DATA SOURCE:` line with the chosen path |
| `folder` | "Choose Folder..." button | `DATA SOURCE:` line with the chosen folder |
| `url` | Text field (`placeholder` supported) | `DATA SOURCE:` line with the URL + fetch hint |
| `paste` | Multiline textarea (`placeholder` supported) | `SOURCE MATERIAL:` block with the pasted text |
| `instructions` | The free-form Brief textarea | `USER BRIEF:` block, exactly like jobs without a `source` |

Convention: put the concrete kinds first and end with one `instructions` option
labeled "Other" or "Instructions" as the escape hatch.

Optional per-option `prompt` overrides the default prompt line; `{value}` is replaced
with the chosen path/URL/text. File and folder picks are granted session read access
automatically, so the hired staff can read them. File options may also declare an
`extensions` array (for example, `["md", "markdown"]`) to filter the native picker.

Malformed files are skipped with a console warning, and a malformed `source` block
degrades to the plain Brief — bad JSON never breaks the tile.

## Specialized translation workflow

The bundled `translate.json` sets `"workflow": "translation"`. That routes the job
to the localization flow instead of the Exhibit creation path. The dialog accepts
multiple files or a folder, a destination folder, one or more BCP-47 target locale
codes, conflict handling, additional instructions, and the same Change AI picker as
the other Jobs.

Translation output is written as `<destination>/<locale-code>/<relative-source-path>`
(for example, `Translations/es-ES/help/getting-started.md`). Source files are never
overwritten. The hired localization lead preserves file structure, formatting,
frontmatter and data keys, code, URLs, and placeholders while translating human-facing
content, then validates output and reports any ambiguity requiring review.

## Specialized computer-setup workflow

The bundled `setup-new-computer.json` sets `"workflow": "computer-setup"` and uses
the standard Study Pack-style source form with one required Markdown document. It
hires a permanent Computer Setup Lead that inventories the current machine, reads
the manifest as the desired state, downloads applications from official sources,
installs them at the requested locations, and verifies each result.

This workflow performs real machine changes. It stops for ambiguous requests,
credentials, licenses, elevation/UAC, unsupported destinations, and reboots rather
than guessing or bypassing operating-system protections.

## Specialized Second Brain workflow

The bundled `second-brain.json` sets `"workflow": "second-brain"`. It hires a
permanent librarian rooted at `{userData}/notes`. Existing notes are immutable raw
sources; the librarian creates and maintains an AI-owned wiki in a user-selected
Notes-relative folder (`{userData}/notes/Second Brain/` by default) with schema, index, log, provenance, and synthesis
pages. Notes under `notes/Private/` and notes carrying `secondBrain.exclude: true`
are excluded from ingestion. It accepts optional run instructions and never falls through to the standard Exhibit
creation path. On completion it creates a presentation-style Second Brain Map Exhibit whose
`navigation.openConstellation` bridge action opens the generated folder in Clairvoyance's native
Constellation view. This workflow is built-in; custom jobs should not use it for unrelated prompts.

## Specialized folder-organizer workflow

`organize-downloads.json` sets `"workflow": "folder-organizer"`. It defaults to the
operating system's Downloads directory but accepts any user-selected folder. The app
builds a bounded shallow inventory (relative path, existing folder context, file type,
size, and modified time) for a modest local model to classify. The AI returns only a
strict move manifest; it never performs filesystem mutations.

With **Preview changes first** enabled, the job runs in the background and creates a
durable local notification whose action opens the review dialog. Applying a reviewed
plan is deterministic app code: it revalidates source fingerprints and root containment,
creates safe directories, and moves files serially without overwriting. Direct mode uses
the same validated manifest and application path, then posts a completion notification.
