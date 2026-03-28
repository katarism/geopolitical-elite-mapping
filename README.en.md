# Geopolitical Key Actors Network Mapping

*An AI-powered platform for analyzing and visualizing geopolitical power networks*

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Python 3.8+](https://img.shields.io/badge/Python-3.8%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)

> **Latest update (2026-03-28)**: The visual editor and AI-assisted generation workflow are now complete.

Let AI handle the heavy lifting of information synthesis, generating an initial map of geopolitical power relationships. You then take over — adding new actors, adjusting relationships, recording the latest developments — turning it into a living knowledge map that grows with you over time.

| Goal | How |
|------|-----|
| Quickly generate a power-relationship map for a given topic | Module 1: AI Automated Modeling |
| Continuously build on the map and accumulate personal knowledge | Module 2: Interactive Visual Editor |

**[Agentic Workflow Live Demo](https://katarism.github.io/geopolitical-elite-mapping/Examples/MiddleEastOil/MiddleEastOil_EliteNetwork_Viewer.html)** (Middle East Oil geopolitical elite network, AI-generated)

**[Sample Analysis Report](https://github.com/katarism/geopolitical-elite-mapping/blob/main/Examples/MiddleEastOil/MiddleEastOil_EliteNetwork_Report.md)**

**Module 1: AI-Generated Interactive Graph**
<img width="1429" height="665" alt="AI-generated geopolitical elite network graph showing faction distribution and relationship edges" src="https://github.com/user-attachments/assets/6dd00815-90cf-45c9-8316-01543ec6684d" />

*AI-generated geopolitical elite network graph showing faction distribution and relationship edges*

**Module 2: Visual Editor**
<img width="1419" height="650" alt="Interactive visual editor interface supporting node add/remove, edge management, and note editing, with cloud save via Git repository" src="https://github.com/user-attachments/assets/4a8ef27e-135d-4ae6-bb76-9a85a2d7fde9" />

*Interactive visual editor interface supporting node add/remove, edge management, and note editing, with cloud save via Git repository*


## Table of Contents

1. [Requirements](#requirements)
2. [Module 1: AI Automated Modeling](#module-1-ai-automated-modeling-agentic-workflow)
3. [Module 2: Interactive Visual Editor](#module-2-interactive-visual-editor)
4. [Project Structure](#project-structure)

## Requirements

| Dependency | Purpose | Installation |
|------------|---------|--------------|
| Python 3.8+ | Graph generation script | [python.org](https://www.python.org/) |
| AI IDE (recommended) | Running the Agentic Workflow | [Cursor](https://www.cursor.com/) / Gravity |

> **Note**: The visual editor (`Editor/editor.html`) requires none of the above — just double-click it in your file browser (Finder / File Explorer) to open it in a browser.

## Module 1: AI Automated Modeling (Agentic Workflow)

An end-to-end generation pipeline built on the project's built-in `SKILL.md`, designed to work with AI IDEs such as **Cursor** or **Gravity**.

### Quick Start

1. **Clone the repository**:
   ```bash
   git clone https://github.com/katarism/geopolitical-elite-mapping.git
   cd geopolitical-elite-mapping
   ```

2. **Open in an AI IDE**: Open the folder in Cursor or Gravity.

3. **Ask the AI**: Enter a geopolitical topic keyword. The AI will automatically complete the following 6 phases:

   ```
   Topic Scoping → Actor Discovery → Relationship Research → Confidence Audit → Visualization → Report Generation
   ```

### Example Inputs

> "Middle East oil geopolitics" / "Indo-Pacific security alliances" / "Global semiconductor supply chain power dynamics"

### Outputs

| File | Description |
|------|-------------|
| `output/[Topic]_EliteNetwork_Viewer.html` | Interactive graph viewer, opens directly in a browser; also the source file for importing into the Editor |
| `output/[Topic]_EliteNetwork_Report.md` | Analysis report (actor profiles, confidence audit table, relationship verification, conclusions) |
| `generate_elite_network.py` | Graph generation script for reproduction and manual iteration |

## Module 2: Interactive Visual Editor

The AI-generated graph is a starting point, not an endpoint. The editor lets you transform it into a **living personal knowledge network** — adding new actors, revising relationship assessments, incorporating the latest developments as your understanding of a topic deepens. The editor is a pure frontend single-file app requiring no build tools or local server.

- **Right-click to edit**: Right-click on empty space to add a node; right-click on a node to edit its faction, notes, or avatar.
- **Intuitive edge management**: Use the sidebar's search-and-dropdown to easily establish complex relationships between actors.
- **Auto faction snapping**: Drag nodes freely — the system automatically assigns them to the appropriate faction group.
- **Git cloud sync**: Changes are synced in real time to your repository via the GitHub API, keeping your data safe.

### Getting Started

1. **Get a Token**: Go to [GitHub Token settings](https://github.com/settings/tokens?type=beta) and create a `Fine-grained Token` with `Contents: Read and Write` permission.

**Option A: Open locally (desktop)**

Double-click `Editor/editor.html` in your file browser (Finder on macOS, File Explorer on Windows) — the browser will open it automatically. Enter your token and repository name, then import the `[Topic]_EliteNetwork_Viewer.html` generated by the Agentic Workflow to get started.

**Option B: Host on GitHub Pages (recommended — works on mobile)**

Opening an HTML file locally does not work on mobile browsers (iOS/Android block `file://` resource loading). Hosting the editor on GitHub Pages gives you a stable URL accessible from any device:

1. Create a new **public repository** on GitHub (e.g. `gem-editor`)
2. Upload `Editor/editor.html` to the root of that repository
3. Go to **Settings → Pages**, set Source to `Deploy from a branch`, Branch to `main`, directory to `/ (root)`, and save
4. After a moment the editor will be available at:
   ```
   https://[your-username].github.io/gem-editor/editor.html
   ```
5. Add the link to your phone's home screen to use it like an app

## Project Structure

```
geopolitical-elite-mapping/
├── SKILL.md                           # AI instruction set (6-phase analysis methodology)
├── generate_elite_network.py          # Core graph generation script
├── viewer_template.html               # Cytoscape.js interactive viewer template
├── index.html                         # Project landing page / viewer entry point
├── cytoscape.min.js                   # Cytoscape.js library (bundled)
├── Editor/
│   └── editor.html                    # Visual editor (pure frontend single-file app)
├── Examples/
│   └── MiddleEastOil/                 # Middle East oil case study (report + viewer)
├── references/                        # Specification documents
│   ├── data_schema.md                 # Data schema (node/edge type definitions)
│   ├── confidence_spec.md             # Confidence annotation spec (HIGH / MED / LOW / INFERRED)
│   ├── visual_spec.md                 # Visual style specification
│   ├── relation_types.md              # Relationship type definitions
│   └── analysis_framework.md         # Geopolitical analysis framework
└── scripts/
    └── wikidata_geopolitics_client.py # Wikidata SPARQL query client
```

---

<div align="center">

**Geopolitical Elite Mapping** — Open-source geopolitical analysis tool

[MIT License](LICENSE) &nbsp;|&nbsp; [Live Demo](https://katarism.github.io/geopolitical-elite-mapping/Examples/MiddleEastOil/MiddleEastOil_EliteNetwork_Viewer.html) &nbsp;|&nbsp; [Submit an Issue](https://github.com/katarism/geopolitical-elite-mapping/issues)

</div>
