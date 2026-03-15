# Calibrate Scoring

You are helping a recruiter calibrate the scoring rubric by reviewing real candidates together. The goal is to align the LLM's scoring judgment with the human's before running a full batch.

## Step 1: Load rubric and source docs

Read:
- `scoring/rubric.md` (required)
- All files in `source_docs/`

If `scoring/rubric.md` is missing or empty, stop and tell the user: "No rubric found. Run `/generate-rubric` first."

## Step 2: Select the run and pull candidates from Supabase

Look in `runs/` for available run folders that contain `intake_output.json`.

- **If there's only one run:** use it automatically.
- **If there are multiple runs:** list them for the user with timestamp and candidate count, and ask which one to calibrate against.
- **If there are no runs:** tell the user "No run output found. Make sure intake and research have been run first." and stop.

Once a run is selected, read the candidate IDs from `intake_output.json` in that run folder, then use the pipeline's Supabase client to fetch full enriched profiles.

```python
import json, sys
from pathlib import Path

# Find and import the pipeline's Supabase client
import importlib
import src.utils.supabase_client as supabase_client

# Read candidate IDs from the selected run
run_dir = Path("<selected run directory>")
intake_path = run_dir / "intake_output.json"
data = json.loads(intake_path.read_text(encoding="utf-8"))
candidate_ids = [
    p["basics"]["supabase_candidate_id"]
    for p in data
    if p.get("basics", {}).get("supabase_candidate_id")
]

# Fetch full profiles from Supabase
rows = supabase_client.fetch_candidates_for_load(candidate_ids)
```

Note: The `cmt-pipeline` package must be installed (`pip install -e .` from the pipeline repo). This makes the `src` package importable from anywhere.

If no candidates are returned from Supabase, tell the user: "No candidate data found in Supabase for this run. Make sure research has completed."

## Step 3: Select 10 candidates for calibration

From the full candidate list, select 10 candidates that represent a diverse range. Try to pick:
- 2-3 that look like strong fits based on the rubric
- 4-5 that are ambiguous or borderline
- 2-3 that look like weak fits or mismatches

Present the list to the user: "I've selected these 10 candidates for calibration. Here's why I picked each one. Want to swap any out?"

Wait for confirmation before proceeding.

## Step 4: Run calibration loop

For each candidate, one at a time:

1. **Present the candidate:** Show a summary of their profile — name, title, company, location, and key data points from their enriched research (YOE, company stages, tech stack, tenure patterns, etc.).

2. **Ask the human first:** "How would you score this candidate (1-10) and why?" Wait for their answer.

3. **Share your score:** After the human responds, share how you would score the candidate against the rubric. Include:
   - Your numeric score (1-10)
   - Your quality grade (reach_out / review / pass)
   - Your reasoning — which rubric criteria they hit or miss

4. **Discuss differences:** If your scores differ by more than 1 point, call it out explicitly:
   - "You gave them a 7, I gave them a 5. The gap is because I weighted X heavily — you're saying Y matters more here. Is that right?"
   - Ask the user to explain their reasoning so you can understand the priorities

5. **Capture the correction:** If the human's score or reasoning reveals something the rubric doesn't capture, note it.

6. **Move to next candidate** after alignment.

## Step 5: Save calibration notes

After all 10 candidates are reviewed, save the calibration results to `scoring/calibration.md` with this structure:

```markdown
# Calibration Notes

## Calibration Summary
[2-3 sentences on what was learned — key priority shifts, rubric gaps, things the human weighted differently than expected]

## Corrections & Insights
[Bulleted list of specific corrections, ordered by importance]
- [Correction 1: what the LLM got wrong and why]
- [Correction 2: ...]

## Calibrated Candidates
| Candidate | Human Score | LLM Score | Final Score | Key Reasoning |
|-----------|------------|-----------|-------------|---------------|
| [Name]    | [X]        | [Y]       | [Z]         | [Brief note]  |

## Priority Overrides
[Any cases where the human said "this matters more/less than the rubric suggests"]
```

## Step 6: Update rubric if needed

If calibration revealed significant gaps or misalignments in the rubric, ask the user:

"Based on calibration, I think these changes to the rubric would improve scoring:
- [Change 1]
- [Change 2]

Want me to update the rubric?"

If they agree, update `scoring/rubric.md` and note at the bottom that it was updated post-calibration.

## Step 7: Wrap up

Tell the user:
- How many candidates were calibrated
- Whether the rubric was updated
- "Calibration is complete. Run `/score` next to score the full candidate batch."

## Important

- Always let the human score first. Do NOT lead with your score — that anchors their judgment.
- Be genuinely open to the human's reasoning. They know the role, the client, and the market better than you do. Your job is to learn from them, not to defend the rubric.
- Disagreements are the most valuable part of calibration. Dig into them.
- Do NOT skip candidates or rush through. Each candidate teaches something.
- Do NOT run the scoring pipeline in this step. Calibration is interactive, scoring is automated.
- If calibration reveals the rubric is fundamentally wrong (e.g., the must-haves are off), suggest the user re-run `/generate-rubric` with the new understanding rather than patching.
