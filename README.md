# Search Repo Template

This repo is the workspace for a single candidate search. Copy it for each new search.

## One-Time Setup (per machine)

1. **Clone the pipeline repo:**
   ```bash
   git clone https://github.com/Clear-Match-Talent/Candidate-List-Pulling.git
   ```

2. **Install the pipeline as a package:**
   ```bash
   cd Candidate-List-Pulling/projects/cmt-pipeline
   pip install -e .
   ```

You only do this once. To update after a pipeline change: `git pull && pip install -e .`

## Per-Search Setup

1. **Create a new repo** from this template on GitHub
2. **Clone it locally** or open it in Claude Code / Cursor

## Workflow

Run these skills in order in Claude Code:

| Step | Command | What it does |
|------|---------|-------------|
| 1 | `/setup-search` | Upload source docs (JD, intake notes, ideal profiles) |
| 2 | `/generate-rubric` | Generate and approve the scoring rubric |
| 3 | *Run intake & research* | `python -m src.cli --search-dir <path> --stages intake,research --csv <csv_path>` |
| 4 | `/calibrate` | Score 10 candidates interactively to calibrate the rubric |
| 5 | `/score` | Run automated scoring on the full batch |
| 6 | `/review` | Walk through the review queue (scores 6-7) and make final decisions |

**Note:** Step 3 (intake & research) is run as a pipeline command, not a skill. It requires a CSV of candidates to process.

## Repo Structure

```
search_config.json          # Search-level config (model, gates, research settings)
source_docs/                # Input documents for this search
  jd.md                     # Job description
  intake_notes.md           # Intake call transcript/notes
  ideal_profiles.md         # Ideal candidate profiles (or ideal_profiles/ for multiple)
scoring/                    # Scoring artifacts
  rubric.md                 # Scoring rubric (generated, human-approved)
  calibration.md            # Calibration notes from interactive review
  heuristics.md             # Cross-role evaluation heuristics
runs/                       # Pipeline run outputs (timestamped folders)
```
