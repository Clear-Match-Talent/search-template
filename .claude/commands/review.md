# Review Candidates

You are helping a recruiter work through the review queue — candidates who scored 6-7 (potential fit, needs human decision).

## Step 1: Load scoring results

Look in `runs/` for available run folders that contain `scoring_results.json`.

- **If there's only one run with scoring results:** use it automatically.
- **If there are multiple runs with scoring results:** list them for the user with timestamp and candidate count, and ask which one to review.
- **If there are no scoring results:** tell the user "No scoring results found. Run `/score` first." and stop.

Read the `scoring_results.json` from the selected run. Filter to only candidates with `quality_grade: "review"` (scores 6-7).

If there are no review candidates, tell the user: "No candidates in the review queue. All candidates were either Reach Out or Pass." and stop.

## Step 2: Present the review queue

Tell the user how many candidates are in the review queue. Then for each candidate, one at a time:

1. **Present the candidate:** Show their name, score, rationale from the scoring run, and key profile details (title, company, location, YOE, notable data points).

2. **Pull additional context if needed:** Use the pipeline's Supabase client to fetch the full enriched profile if the scoring results don't have enough detail (the `cmt-pipeline` package must be installed):
   ```python
   from src.utils import supabase_client
   rows = supabase_client.fetch_candidates_for_load(["<candidate_id>"])
   ```

3. **Ask for a decision:** "What's your call on this candidate?"
   - **Upgrade to Reach Out** — move to outreach
   - **Keep as Review** — stay in queue for later
   - **Downgrade to Pass** — not a fit

4. **Capture reasoning:** Ask briefly why, especially for upgrades and downgrades. This feeds future calibration.

5. **Move to next candidate.**

## Step 3: Apply decisions

After all review candidates are processed, summarize the decisions:
- [X] upgraded to Reach Out
- [X] kept as Review
- [X] downgraded to Pass

Then update Supabase for each candidate whose status changed (the `cmt-pipeline` package must be installed):

```python
from src.utils import supabase_client

# For upgrades (review -> reach_out)
supabase_client.update_candidate_enrichment("<candidate_id>", {
    "quality_grade": "reach_out",
    "status": "active_tracking",
    "is_core": True,
    "last_updated": "<now_iso>",
})

# For downgrades (review -> pass)
supabase_client.update_candidate_enrichment("<candidate_id>", {
    "quality_grade": "pass",
    "status": "passed",
    "is_core": False,
    "last_updated": "<now_iso>",
})
```

Log each decision to the evaluation log:
```python
supabase_client.log_evaluation([{
    "candidate_id": "<candidate_id>",
    "stage": "review",
    "action": "<upgraded|kept|downgraded>",
    "reason": "<user's reasoning>",
    "source_list": "<search repo name>",
}])
```

## Step 4: Save review results

Save a summary of all review decisions to `runs/<selected_run>/review_results.json` with the structure:
```json
[
  {
    "candidate_id": "...",
    "name": "...",
    "original_score": 7,
    "decision": "upgraded",
    "new_grade": "reach_out",
    "reasoning": "..."
  }
]
```

## Step 5: Wrap up

Tell the user:
- Total reviewed, upgraded, kept, downgraded
- "Review is complete. Reach Out candidates are ready for outreach. Any candidates kept as Review can be revisited later."
- If several candidates were upgraded or downgraded for similar reasons, suggest: "It looks like a pattern — [describe pattern]. This might be worth adding to the calibration notes for future scoring runs."

## Important

- Present one candidate at a time. Don't overwhelm with the full list.
- Do NOT change scores for Reach Out (8-10) or Pass (1-5) candidates. This skill is only for the Review queue.
- Do NOT re-run the scoring pipeline in this step.
- If the user wants to see a Reach Out or Pass candidate, they can ask — show them the profile but note that changing those scores is outside the review workflow.
