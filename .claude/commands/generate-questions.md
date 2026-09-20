Draft the typed-scorer question set for this search from the source documents, then show it for review.

This replaces `/generate-rubric` for searches whose `search_config.json` has `scoring.engine` set to `both` or `new`. The question set plus `scoring/composition.json` IS the rubric; `scoring/rubric.md` is rendered from them and is read-only.

Run from the pipeline repo:

```bash
cd /c/Users/mdsin/projects/cmt-pipeline
/c/Users/mdsin/bin/doppler run -- py scripts/draft_questions.py --search-dir {this search repo path}
```

For a multi-lane pool, pass one `--lane name=source_docs/jd_name.md` per lane.

What it does:
1. Reads `source_docs/*.md` and the research fields this search's dossier will contain.
2. One MiniMax call drafts `scoring/questions.json`: `role_type`, `depth`, `scale`, one `ev_<lane>` per lane, `measured_outcomes`, `named_artifact`. Every score question has exactly four concrete levels. Nothing numeric is asked; years, tenure and counts are computed in code.
3. Writes `scoring/composition.json` from the vertical defaults (thresholds). These are NOT drafted; they are fitted later.
4. Renders `scoring/rubric.md` so the whole thing is readable in one place.

Then display `scoring/rubric.md` and ask Matt to review the **question wording and levels only**. Do not ask him to review thresholds; those are tuned by `py scripts/calibrate_jev.py select` + `fit --labels` once research exists.

If the draft fails validation the command prints why and leaves `scoring/questions_draft_invalid.*.json`; fix the source docs or re-run, do not hand-patch the JSON.
