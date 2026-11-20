# Clairvoyance Job Types

Job types for the [Clairvoyance](https://www.clairvoyanceai.com) **Jobs** HomeDesk tile: reusable tasks you hand to a Staff member.

## Install

Copy a `.json` file into `config/jobs/` in your Clairvoyance data folder (Windows: `%APPDATA%\clairvoyance\config\jobs\`), then restart Clairvoyance. Give your own variations a new file name, because files that ship with the app are refreshed on launch.

## Contents

| File | Name | Description |
| --- | --- | --- |
| `auditor.json` | Auditor | A structured, shareable report card — findings, severity, and recommended fixes. |
| `classify-this.json` | Classify This | Classify a file or folder — add curated metadata: type, tags, summary, and entities. |
| `competitive-analysis.json` | Competitive Analysis | Options or competitors scored side by side, with positioning and talk tracks. |
| `concept-explainer.json` | Concept Explainer | A visual, hands-on simulation of a concept — see it work, not just read about it. |
| `dashboard.json` | Dashboard | Turn a spreadsheet, CSV, or dataset into an interactive, filterable dashboard. |
| `data-insights.json` | Data Insights | Point at data and get an explorable insights report — trends, outliers, and the story behind the numbers. |
| `game-maker.json` | Game Maker | Describe a game or hand it a design doc, and get a complete, playable game as an exhibit. |
| `local-classify-folder.json` | Classify Folder (Local) | Classify every file in a folder with metadata (type, summary, tags) using a local model. |
| `local-data-exhibit.json` | Data Exhibit (Local) | Build an Exhibit with charts and KPIs from a CSV, SQL dump, SQLite, or JSON file using a local model. |
| `local-summarize-file.json` | Summarize File (Local) | Summarize a long file into a Report using a local model — the host chunks the file and asks the model small questions. |
| `local-web-report.json` | Web Report (Local) | Turn a web page or a saved page/file into a structured Report using a local model. |
| `organize-downloads.json` | Organize Downloads | Organize Downloads—or another folder—without deleting or overwriting files. |
| `pitch-deck.json` | Pitch Deck | A navigable slide presentation built from your brief. |
| `second-brain.json` | Second Brain | Compile your existing Clairvoyance notes into a persistent, AI-maintained wiki. |
| `setup-new-computer.json` | Setup New Computer | Download, install, and verify the software listed in a Markdown setup manifest. |
| `study-pack.json` | Study Pack | Flashcards, quizzes, and matching games generated from any source material. |
| `translate.json` | Translate | Translate files into one or more languages while preserving structure, formatting, and localization tokens. |
| `what-if-model.json` | What-If Model | A live calculator where changing the inputs instantly recomputes the outputs. |

## File format

See [FORMAT.md](FORMAT.md).

## Make your own

Use any item here as a starting point. To list yours in the app's community catalog, put it in your own GitHub repository and open a pull request on [clairvoyance-releases](https://github.com/clairvoyanceapp/clairvoyance-releases).

## Feedback

This repository holds content only. Report app bugs and feature requests through the Clairvoyance app or [clairvoyanceai.com](https://www.clairvoyanceai.com).

## License

See [LICENSE.md](LICENSE.md).
