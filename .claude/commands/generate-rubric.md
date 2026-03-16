# Generate Rubric

You are helping a recruiter generate a scoring rubric for candidate evaluation. The rubric will be used by an LLM to score candidates on a 1-10 scale.

## Step 1: Read all source documents

Read everything in the `source_docs/` directory:
- `source_docs/jd.md` (required)
- `source_docs/intake_notes.md` (required)
- `source_docs/ideal_profiles.md` or `source_docs/ideal_profiles/` (if present)
- Any additional documents in `source_docs/`

If `jd.md` or `intake_notes.md` are missing or empty, stop and tell the user: "Source docs are missing. Run `/setup-search` first."

## Step 2: Generate the rubric

Based on everything in the source docs, generate a structured scoring rubric and save it to `scoring/rubric.md`. The rubric must have these sections:

```markdown
# Scoring Rubric: [Role Title] at [Company]

## Role Context
Brief summary of the role, team, company stage, and what matters most to the hiring manager. This gives the scoring LLM the "why" behind the criteria.

## Must-Haves
Requirements that are non-negotiable. Candidates missing these should score 1-4 regardless of other strengths. Be specific — not "strong engineer" but "5+ years backend experience in distributed systems."

## Strong Signals
Attributes that strongly indicate fit. These are the difference between a 7 and a 9. Drawn from what the hiring manager emphasized in the intake call and patterns from ideal profiles.

## Nice-to-Haves
Attributes that are a plus but not required. These can bump a candidate up a point but their absence shouldn't count against them.

## Red Flags / Exclusions
Patterns that indicate a candidate is not a fit. Be specific about what actually disqualifies vs. what just looks bad on paper but might be fine in context.

## Score Guide
| Score | Meaning |
|-------|---------|
| 9-10 | Exceptional fit. Matches must-haves and multiple strong signals. Prioritize for outreach. |
| 8 | Strong fit. Matches must-haves and some strong signals. Reach out. |
| 5-7 | Potential fit or borderline. Needs human review. Includes candidates worth airtag tracking or cross-role targeting. |
| 4 | Weak fit. Missing key experience depth or has concerning signals. Pass unless context changes. |
| 1-3 | Not a fit. Missing must-haves or has red flags. Clear pass. |
```

## Step 3: Present for review

After saving the rubric, present it to the user in full. Then ask:

"Here's the rubric I generated. Please review it:
- Is anything missing from the must-haves?
- Are any of the red flags wrong or too aggressive?
- Is there anything the hiring manager cares about that I didn't capture?
- Anything you'd change or add?

Once you're happy with it, say 'approved' and I'll finalize it. Or tell me what to change."

## Step 4: Iterate and finalize

If the user requests changes, update `scoring/rubric.md` and present the updated version. Repeat until the user approves.

Once approved, tell them: "Rubric is finalized. Run `/calibrate` next to calibrate the scoring with real candidates."

## Important

- The rubric should be specific to this role and client, not generic. Use the hiring manager's own language from the intake notes wherever possible.
- Prioritize the intake call notes over the JD — the intake call reflects what the hiring manager actually cares about, the JD is often aspirational or written by HR.
- If ideal profiles are provided, use them to identify patterns — what do these candidates have in common? What makes them ideal?
- Do NOT run any pipeline commands or score any candidates in this step.
- Do NOT skip the human review. The rubric must be approved before moving on.
