# Score Candidates

You are helping a recruiter run the automated scoring pipeline against a batch of candidates.

## Step 1: Pre-flight checks

Verify the following files exist in this search repo:
- `scoring/rubric.md` — required. If missing, tell the user: "No rubric found. Run `/generate-rubric` first."
- `scoring/calibration.md` — optional but recommended. If missing, warn: "No calibration notes found. Scoring will work but results will be better if you run `/calibrate` first. Continue anyway?"

Also check that `runs/` contains at least one run with `intake_output.json`.

- **If there's only one run:** use it automatically.
- **If there are multiple runs:** list them for the user with timestamp and candidate count, and ask which one to score.
- **If there are no runs:** tell the user "No run output found. Make sure intake and research have been run first." and stop.

Wait for confirmation before proceeding.

## Step 2: Run the scoring pipeline

Run the pipeline (the `cmt-pipeline` package must be installed):

```bash
python -m src.cli --search-dir "<this search repo absolute path>" --stages score
```

Let the output stream so the user can see progress as each candidate is scored.

## Step 3: Summarize results

After scoring completes, read the `scoring_results.json` from the new run folder in `runs/`. Present a summary:

- Total candidates scored
- Breakdown by grade:
  - **Reach Out (8-10):** [count] candidates
  - **Review (6-7):** [count] candidates
  - **Pass (1-5):** [count] candidates
- List the Reach Out candidates by name, score, and one-line rationale
- Note any candidates that failed to score (errors)

## Step 4: Next steps

Tell the user:
- "Scoring is complete. Here's what to do next:"
- "Run `/review` to walk through the Review queue (6-7 scored candidates) and make final decisions."
- "Reach Out candidates (8-10) are ready for outreach — they've been marked as active_tracking in the system."
- "Pass candidates (1-5) have been marked as passed. No action needed."

## Important

- Do NOT modify the rubric or calibration files in this step.
- Do NOT re-score candidates that have already been scored in this run.
- If the pipeline fails, show the error to the user and help them debug. Common issues: missing ANTHROPIC_API_KEY env var, Supabase connection issues, rubric file not found.
- If the user wants to re-score after adjusting the rubric, they can run `/score` again. A new run folder will be created.
